# vue-phone-input

This vue component is inspired by [vue-phone-number-input](https://www.npmjs.com/package/vue-phone-number-input).
When using it, I found it's not very fit for me, missing places are list below:
- The countries can't be filterd or exclued
- The related lib `libphonenumber-js` have [validation issue](https://github.com/LouisMazel/vue-phone-number-input/issues/127).
- The theme is limited.

So I am trying to cover these functions and create a new one for myself.
If it is helpful, feel free to use it, or if you find any issue, please fire a bug.

Dependency libs: 
- [libphonenumber-js](https://www.npmjs.com/package/libphonenumber-js)
- [vue-country-flag](https://github.com/P3trur0/vue-country-flag)
## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
