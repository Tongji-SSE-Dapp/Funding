<template>
  <div class="footer">
    <ul v-for="list in category" class="list">
      <li class="title">{{ list.title }}</li>
      <li v-for="item in list.linkList">
        <a :href="item.link" target="_blank">{{ item.name }}</a>
      </li>
    </ul>
  </div>
</template>

<script>
import {defineComponent, readonly} from 'vue'

export default defineComponent({
  name: 'mFooter',
  setup() {
    let category = readonly([
      {
        title: '技术支持',
        linkList: [
          {name: 'Vue', link: 'https://cn.vuejs.org/'},
          {name: 'Ant Design Vue', link: 'https://2x.antdv.com/docs/vue/introduce-cn/'}
        ]
      }
    ])
    return { category }
  }
})
</script>

<style scoped>
.footer {
  display: flex;
  padding: 2em 5%;
  background: linear-gradient(135deg, #0f4c81, #1c6ea4);
  border-top: 2px solid #1c6ea4;
  box-shadow: 0 -5px 15px rgba(28,110,164,0.5);
  transition: all 0.5s ease;
  position: relative;
  z-index: 10;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.footer:before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle at bottom right, rgba(255,255,255,0.1), transparent 70%);
  z-index: -1;
}

.footer .list {
  list-style: none;
  margin-right: 5%;
  transition: all 0.3s;
}

.footer .list:hover {
  transform: translateY(-5px);
}

.footer .list li {
  margin-bottom: 0.5em;
}

.footer .list li.title {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 0.5em;
  text-transform: uppercase;
  letter-spacing: 1px;
  position: relative;
  background: linear-gradient(45deg, #ffffff, #e6f7ff);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 0 0 5px rgba(255,255,255,0.7), 0 0 10px rgba(230,247,255,0.5);
}

.footer .list li.title:after {
  content: "";
  display: block;
  width: 30%;
  height: 3px;
  background: #fff;
  margin-top: 0.3em;
  border-radius: 2px;
  opacity: 0.9;
}

.footer .list li a {
  color: #fff;
  font-size: 16px;
  font-weight: 600;
  text-decoration: none;
  transition: color 0.3s, text-shadow 0.3s;
  position: relative;
  text-shadow: 0 0 3px rgba(255,255,255,0.5);
}

.footer .list li a:hover {
  color: #e6f7ff;
  text-shadow: 0 0 8px #e6f7ff, 0 0 15px #e6f7ff;
}
</style>
