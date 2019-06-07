# BinanceChain Notes

```
npm install @binance-chain/javascript-sd
npm install @angular/cli
npm install os
npm install path
npm install stream
```

package.json `scripts`
```
"postinstall": "node patch-webpack.js"
```

patch-webpack.js
```
const fs = require('fs');
const f = 'node_modules/@angular-devkit/build-angular/src/angular-cli-files/models/webpack-configs/browser.js';

fs.readFile(f, 'utf8', function (err,data) {
  if (err) {
    return console.log(err);
  }
  let result = data.replace(/node: {crypto: true, stream: true},/g, "node: {crypto: true, stream: true, fs: 'empty', net: 'empty'},");

  fs.writeFile(f, result, 'utf8', function (err) {
    if (err) return console.log(err);
  });
});
```



In `app.component.ts` file imports section:
```
declare const require: any;
const BnbApiClient = require('@binance-chain/javascript-sdk');
```

In the class section for wallet commands:
```
const privatekey = BnbApiClient.crypto.generatePrivateKey();
const address = BnbApiClient.crypto.getAddressFromPrivateKey(privateKey);
```

For transactions:
```

const dataChain = {
name: 'Binance-Chain-Nile', api: 'https://testnet-dex.binance.org/'
};

const privatekey = BnbApiClient.crypto.generatePrivateKey();
const address = BnbApiClient.crypto.getAddressFromPrivateKey(privateKey);

  const bnbClient = new BnbApiClient(dataChain.api);
  bnbClient.initChain(dataChain.name);
  bnbClient.setPrivateKey(privateKey);

  const balanceJSON = await bnbClient.getBalance(address)
  .then(result => {
    const bnbArray = result.find(bnbArray => bnbArray.symbol === 'BNB');
    const balBNB = bnbArray.free;

    const canArray = result.find(canArray => canArray.symbol === 'CAN-067');
    this.balance = canArray.free;
    this.staked = canArray.frozen

    if (result.status === 200) {
      console.log('success', result.result[0].hash);
    } else {
      console.log('result', result);
    }

    if (result.status === 429) {
      console.log('Wait 2 minutes');
    }

    // return bal;
  })
    .catch((error) => {
      console.error('error', error);
    });


```


## Angular | Webpack

Can't resolve fs error: https://github.com/webpack-contrib/css-loader/issues/447

Add to `webpack.config.js`:
```
node: {
  fs: 'empty'
},
```

Adding a JS loader, use Babel:
```
npm install --save-dev "babel-loader@^8.0.0-beta" @babel/core @babel/preset-env
```

Add to `webpack.config.js`:
```
{
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
        loader: 'babel-loader',
         }
}
```
