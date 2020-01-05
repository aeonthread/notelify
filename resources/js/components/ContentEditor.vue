<template>
  <div>
    <template v-for="block in content.body">
      <component :is="block.component" :block="block" :key="block._uid"></component>
    </template>
</div>
</template>
<script>

import TextBlock from "./TextBlock";
export default {
  name: 'ContentEditor',
  components: {
    TextBlock
  },
  data() {
    return {
      content: {
        body: [{
          _uid: "BUY6Drn9e1",
          component: "TextBlock",
        }
        ]
      }
    }
  },
  methods: {
    update(e) {
      this.html = e.target.innerHTML;
      console.log(this.getPos());
    },
    updateDOM(new_html, e) {
      this.html = new_html;
      e.target.innerHTML = new_html;
    },
  },
}
</script>

