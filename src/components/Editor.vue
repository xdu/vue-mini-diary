<template>
  <div>
    {{ value.length }}
    <div id="vditor" />
  </div>
</template>

<script>
import Vditor from "vditor";

import "vditor/dist/index.css";

export default {

  name: "Editor",
  props: {
    value : {
      type: String,
      default: ''
    }
  },

  watch: {
    value: function(val) {
      this.contentEditor.setValue(val)
    }
  },

  data: () => {
    return {
      contentEditor: null,
    };
  },

  mounted() {
    this.contentEditor = new Vditor("vditor", {
      value: this.value,
      height: 450,
      lang: "en_US",
      icon: "ant",
      toolbarConfig: {
        pin: true,
      },
/*      toolbar: [
        {
          hotkey: "⌘-⇧-S",
          name: "sponsor",
          tipPosition: "s",
          tip: "成为赞助者",
          className: "right",
          icon: '<svg><use xlink:href="#vditor-icon-code"></use></svg>',
          click() {
            alert("捐赠地址：https://ld246.com/sponsor");
          },
        },
      ],*/
      cache: {
        enable: false,
      }
    });
  },
};
</script>
