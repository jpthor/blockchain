# BinanceChain Notes

```
npm install @binance-chain/javascript-sd
npm install @angular/cli
npm install os
npm install path
npm install stream
npm install libudev-dev libusb-dev usbutils
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
  let result = data.replace(/node: false/g, "node: {crypto: true, stream: true, fs: 'empty', net: 'empty'}");

  fs.writeFile(f, result, 'utf8', function (err) {
    if (err) return console.log(err);
  });
});
```



In `app.component.ts` file imports section:
```
declare const require: any;
var BnbApiClient = require('@binance-chain/javascript-sdk');
```

In the class section:
```
public.privatekey = BnbApiClient.crypto.generatePrivateKey();
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
