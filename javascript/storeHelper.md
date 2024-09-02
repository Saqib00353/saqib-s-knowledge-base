# StoreHelper

```javascript
const StoreHelper = {
  // get a item from localstorage
  get(key) {
    let value = window.localStorage.getItem(key)
    if (!value) return null
    return value
  },
  // set item to localstorage
  set(key, value) {
    window.localStorage.setItem(key, value)
  },
  // delete item from localstorage
  delete(key) {
    window.localStorage.removeItem(key)
  },
}

export { StoreHelper }
```
