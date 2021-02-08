<template>
  <div>
    {{ value.length }}
    <div id="vditor" />
  </div>
</template>

<script>
import Vditor from "vditor";
import path from 'path'
import sharp from 'sharp'
import "vditor/dist/index.css";

const path_img_dir = "C:\\Users\\xdu\\Workspace\\hexo\\source"

export default {

  name: "Editor",
  props: {
    value : {
      type: String,
      default: ''
    }
  },

  watch: {
    value: async function(val) {

      const regex = /!\[.*\]\((.*)\)/i
      const m = regex.exec(val)

      if (m) {

        console.log("image found : " + m[0])

        // load the image
        const imgpath = path.join(path_img_dir, m[1]);
        console.log("Image file : " + imgpath) 

        let buf = await sharp(imgpath).resize({width: 450, height: 400, fit: sharp.fit.contain}).toBuffer()
        let base64 = buf.toString('base64')

        val += '\n\n![](data:image/*;base64,' + base64 + ')'
      }

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
