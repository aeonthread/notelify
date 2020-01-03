<template>
  <div>
    <template v-for="block in content.body">
      <component :is="block.component" :block="block" :key="block._uid"></component>
    </template>
</div>
</template>

<script>
import Foo from "./Foo";
export default {
  name: 'ContentEditor',
  components: {
    Foo
  },
  data() {
    return {
      content: {
        body: [{
          _uid: "BUY6Drn9e1",
          component: "foo",
          headline: "Foo"
        },
        {
          _uid: "X1JAfdsZxy",
          component: "foo",
          headline: "Another headline"
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
    line_number_up(e) {
      let line_number_matches = this.html.match(/<br>/g);
      let line_number = line_number_matches.length;
      this.line_number = line_number;
    },
    updateDOM(new_html, e) {
      this.html = new_html;
      e.target.innerHTML = new_html;
    },
  },
}
</script>

