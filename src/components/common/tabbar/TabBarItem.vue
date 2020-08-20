<template>
  <div class="tar-bar-item" @click="activeChange">
    <slot v-if="!activeIcon" name="item-icon"></slot>
    <slot v-else name="item-icon-active"></slot>
    <div :style="{color:activeColor}">
      <slot name="item-text"></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TabBarItem',
  props: {
    path: {
      type: String
    },
    itemColor: {
      type: String,
      default: '#FF5777'
    }
  },
  // data() {
  //   return{
  //     active: false
  //   }
  // },
  methods: {
    activeChange() {
      this.active = true
      this.$router.replace(this.path)
    }
  },
  computed: {
    activeIcon(){
      return this.$route.path.indexOf(this.path) != -1 ? true : false
    },
    activeColor() {
      return this.activeIcon ? this.itemColor : ''
    }
  }
}
</script>

<style>
  .tar-bar-item {
    flex: 1;
    text-align: center;
    font-size: 14px;
  }
  .tar-bar-item img {
    width: 24px;
    height: 24px;
    margin-top: 3px;
    margin-bottom: 3px;
    vertical-align: middle;
  }
  /* .active {
    color: red;
  } */
</style>