---
sidebar_position: 1
---

# Lifecycle

## Initializing

In order to initialize the native modules, call `initConnection()` early in the lifecycle of your application. This should be done at a top-level component as the library caches the native connection. Initializing just before you needed is discouraged as it incurrs on a performance hit. Calling this method multiple times without ending the previous connection will result in an error. Not calling this method will cause other method calls to be rejected as connection needs to be established ahead of time. 

```javascript
import * as RNIap from 'react-native-iap';

componentDidMount() {
    RNIap.initConnection();
    ...
```

## Ending Connection

In order to release the resources, call `endConnection()` when you no longer need any interaction with the library.

```javascript
componentWillUnmount() {
  ...
  RNIap.endConnection();
}
```

## Dos and Don'ts
You shoud not call `initConnection` and `endConnection` every time you need to interact with the library. This is considered an anti-pattern as it consumes more time, resources and could lead to undesired side effects such as many callbacks.

### :white_check_mark: DO:
```javascript
import * as RNIap from 'react-native-iap';

componentDidMount() {
  await RNIap.initConnection();

  await RNIap.getProducts(productIds)
  ...
}

buyProductButtonClick() {
  //startPurchaseCode
}

subscribeButtonClick() {
  //startPurchaseCode
}


componentWillUnmount() {
  ...
  RNIap.endConnection();
}
```

### :x: DON'T :
```javascript
import * as RNIap from 'react-native-iap';

componentDidMount() {
  ...
}

const buyProductButtonClick = async() => {
  await RNIap.initConnection();

  await RNIap.getProducts(productIds)
  // Purchase IAP Code
  ...
    await RNIap.endConnection();
}

const subscribeButtonClick = async() => {
  await RNIap.initConnection();

    await RNIap.getProducts(productIds)
    //Purchase Subscription Code
    ...
    await RNIap.endConnection();
}

componentWillUnmount() {
  ...
}
```

## Long lived connections (Android)
Although using hooks and tying the connection lifecycle to a component is recommended, you might want to handle connections separate from the UI layer (e.g. Redux Sagas), in this case you need to take extra care of the connection lifecycle, the connection might break and the only way to reconnect is to call the `RNIap.endConnection()` method and then `RNIap.initConnection()` again, this creates a new internal instance of a billing client and is the only way to reconnect to the Play Store services.

You can check if the connection is valid by calling the `RNIap.isReadyAndroid()` method, if it returns `false` a call to `initConnection` is necessary.