<img src="https://www.mintjams.jp/img/cr.svg" alt ="" width="64">

# vue-avatar
A reusable Avatar component for Vue.js 2.x used by webtop applications.

## Installation

```sh
npm install --save-dev @mintjamsinc/vue-avatar
```

## Usage

```vue
<Avatar :authorizable="authorizable"/>
```

```js
import Avatar from '@mintjamsinc/vue-avatar';

export default {
  components: {
    Avatar: Avatar,
  },
  computed: {
    authorizable() {
      return window.Webtop.userClient.newAuthorizable(this.$store.state.user);
    },
  },
}
```

## License

[MIT](https://opensource.org/licenses/MIT)

Copyright (c) 2021 MintJams Inc.