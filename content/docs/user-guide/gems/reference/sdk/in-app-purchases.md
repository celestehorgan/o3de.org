---
linkTitle: In App Purchases
title: In App Purchases Gem
description: The In App Purchases Gem provides functionality for in app purchases in Open 3D Engine (O3DE) projects.
toc: true
---

The In-App Purchases Gem provides functionality for in app purchases in an Open 3D Engine (O3DE) project. Platform-specific information is handled by the provided API, allowing you to use a single implementation of the In-App Purchases Gem for both Android and iOS.

{{< note >}}
For Android, you must install the Google Play billing library from the Android SDK Manager. You can find this option under the extras section.
{{< /note >}}

## Handling Requests for Queries and Purchases

You can access the API through code by connecting to the EBus:

`EBUS_EVENT(InAppPurchases::InAppPurchasesRequestBus, Initialize);`

To use the API, include the `InAppPurchasesBus.h` header file.

You can also access the API through Script Canvas.

| Method | Usage | Parameters |
| --- | --- | ---|
| `Initialize` | Call this method first for all platforms. This method handles all necessary setup before the API can be used. | None |
| `QueryProductInfo` | Use this method when the product IDs are shipped with the game. This method looks for a product ID file (`product_ids.json` on Android or `product_ids.plist` on iOS) and retrieves product details using the IDs that are specified in the file. | None |
| `QueryProductInfoByIds` | Use this method to retrieve product details if the product IDs are provided at runtime, for example if they are retrieved from a server at runtime. | `AZStd::vector<AZStd::string>& productIds` |
| `QueryProductInfoById` | Use this method to retrieve product details if the product IDs are provided at runtime, for example if they are retrieved from a server at runtime. Use this method to retrieve details for a single product only. | `AZStd::string& productId` |
| `QueryPurchasedProducts` | Use this method to query the Google Play Store for products that were already purchased by the signed-in user. On iOS, this method reads the receipt that is stored on the device and lists the items purchased by the signed-in user. | None |
| `RestorePurchasedProducts` | Use this method to restore purchases that were made by the user. This applies to purchases made on a different device, where the current device does not have receipts stored locally. This method is supported on iOS only. | None |
| `ConsumePurchase` | Call this method for all purchases that are consumable, such as virtual currencies, health, and ammo. This method requires the purchase token provided by the Google Play Store when the product was purchased. This method is required and supported on Android only. | `AZStd::string& purchaseToken` |
| `FinishTransaction` | Call this method at the end of a transaction. This method requires the transaction ID provided by the iOS App Store when the product was purchased. It also accepts a boolean parameter to indicate whether or not to download content that is hosted on Apple servers. If this method is not called, the transaction will be reported each time the game is restarted. This method is required and supported on iOS only. | `AZStd::string& transactionId | bool downloadHostedContent` |
| `PurchaseProduct` | Use this method to request to purchase a product from the Google Play Store or the iOS App Store. This method requires the product ID of the product being purchased. | `AZStd::string& productId` |
| `PurchaseProductWithDeveloperPayload` | Use this method to request to purchase a product from the Google Play Store or the iOS App Store. This method requires the product ID of the product being purchased. It accepts an additional parameter for the developer payload, which is used by Android to associate a purchase with a user account. When you request purchased products, you can use the developer payload to determine if the signed-in user made the purchase. On iOS, the user account is used in fraud detection. | `AZStd::string& productId | AZStd::string& developerPayload` |
| `GetCachedProductInfo` | Use this method to return product details that are stored in a cache each time there is a query for information. The cache only stores product details that were retrieved during the previous call to `QueryProductInfo/QueryProductInfoByIds/QueryProductInfoById`. | None |
| `GetCachedPurchasedProductInfo` | Use this method to return details for purchased products that are stored in a cache. The cache only stores details for purchased products that were retrieved during the previous call to `QueryPurchasedProducts`. | None |
| `ClearCachedProductDetails` | Use this method to clear the product details that were cached by the previous call to query product details. | None |
| `ClearCachedPurchasedProductDetails` | Use this method to clear the details for purchased products that were cached by the previous call to query details for purchased products. | None |

## Handling Responses to Queries and Purchases

When a user makes a query or a purchase, the API sends the request to Apple or Google servers. Once a response is received, the API broadcasts the response on the `InAppPurchasesResponse` bus.

To handle responses to queries or purchases, overload the functions provided in the class in the `InAppPurchasesResponseBus.h` file.

| Method | Usage | Parameters |
| --- | --- | ---|
| `ProductInfoRetrieved` | This method is called when product information is retrieved for all requested products. Product information includes product ID, name, description, price, and more. Depending on the platform, the provided pointers must be cast to the appropriate type (`ProductDetailsAndroid` or `ProductDetailsApple`). | `const AZStd::vector<AZStd::unique_ptr<ProductDetails const> >& productDetails` |
| `PurchasedProductsRetrieved` | This method is called when details are retrieved for all purchased products. Details for purchased products include product ID, transaction ID, transaction time, and more. Depending on the platform, the provided pointers must be cast to the appropriate type (`PurchasedProductDetailsAndroid` or `PurchasedProductDetailsApple`). | `const AZStd::vector<AZStd::unique_ptr<PurchasedProductDetails const> >& purchasedProductDetails` |
| `NewProductPurchased` | This method is called when a new product is successfully purchased. | `const PurchasedProductDetails* purchasedProductDetails` |
| `PurchaseCancelled` | This method is called when a purchase is canceled. | `const PurchasedProductDetails* purchasedProductDetails` |
| `PurchaseRefunded` | This method is called when a purchase is refunded. | `const PurchasedProductDetails* purchasedProductDetails` |
| `PurchaseFailed` | This method is called when a purchase fails. | `const PurchasedProductDetails* purchasedProductDetails` |
| `HostedContentDownloadComplete` | This method is called when content that is hosted on Apple servers is downloaded successfully. The transaction ID and download path are provided. | `const AZStd::string& transactionId | AZStd::string& downloadedFileLocation` |
| `HostedContentDownloadFailed` | This method is called when content that is hosted on Apple servers fails to download. The transaction ID and content ID of the failed content download are provided. | `const AZStd::string& transactionId | const AZStd::string& contentId ` |