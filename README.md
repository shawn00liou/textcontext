# Quasar App (textcordova)

A Quasar Framework app

## Install the dependencies

```bash
yarn
```

### Start the app in development mode (hot-code reloading, error reporting, etc.)

```bash
quasar dev
```

### Lint the files

```bash
yarn run lint
```

### Build the app for production

```bash
quasar build
```

### Customize the configuration

See [Configuring quasar.conf.js](https://quasar.dev/quasar-cli/quasar-conf-js).

#### yarn build

keytool -genkey -v -keystore recordmy.keystore -alias recordmy -keyalg RSA -validity 30000

aug

jarsigner -verbose -keystore recordmy.keystore -signedjar app-release.apk app-release-unsigned.apk recordmy

~/Library/Android/sdk/build-tools/30.0.2/
./zipalign -v 4 ~/vuework/sitepackage.frontend.software/textcordova/dist/cordova/android/apk/release/app-release.apk app-release.apk
