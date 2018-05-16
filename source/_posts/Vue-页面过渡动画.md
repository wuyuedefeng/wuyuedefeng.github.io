title: Vue 页面过渡动画
author: wsen
date: 2018-05-16 14:00:54
tags:
---
```javascript
<template>
  <div id="app">
    <transition name="page-toggle-transition">
      <router-view class="page-toggle-transition"/>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style lang="scss">
  #app {
    .page {
      position: absolute;
      left: 0; right: 0; top: 0; bottom: 0;

      &.page-top-transition {
        transition: opacity .3s ease;
        &.page-top-transition-enter, &.page-top-transition-leave-to {
          opacity: 0;
        }
      }

      &.page-push-transition {
        transition: transform .3s ease, opacity .3s ease;
        &.page-push-transition-enter {
          transform: translateX(100%);
        }
        &.page-push-transition-leave-to {
          opacity: 0;
        }
      }

      &.page-pop-transition {
        transition: transform .3s ease, opacity .3s ease;
        &.page-pop-transition-enter {
          opacity: 0;
        }
        &.page-pop-transition-leave-to {
          transform: translateX(100%);
        }
      }

      &.page-toggle-transition {
        transition: transform .3s ease, opacity .3s ease;
        &.page-toggle-transition-enter {
          transform: translateX(100%);
        }
        &.page-toggle-transition-leave-to {
          transform: translateX(100%);
          opacity: 0;
        }
      }
    }
  }
</style>
```