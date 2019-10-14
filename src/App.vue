<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
  .fm-cover-bg {
    z-index: 200;
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
}

.fm-picker-box {
    position: fixed;
    z-index: 300;
    background: #fff;
    bottom: 0;
    left: 0;
    right: 0;
}

.fm-picker-header {
    background-color: #f8f8f8;
    height: 44px;
    line-height: 44px;
    overflow: hidden;
    text-align: center;
    font-size: 16px;
}

.fm-picker-cancel {
    float: left;
    padding: 0 20px;
    color: #999;
    font-size: 16px;
}

.fm-picker-confirm {
    float: right;
    padding: 0 20px;
    color: #EF8200;
    font-size: 16px;
}

.fm-picker-content {
    width: 100%;
    margin: 0 auto;
    background: #fff;
    overflow: hidden;
    height: 170px;
    display: flex;
    justify-content: center;
}

.fm-picker-top {
    width: 100%;
    border: none;
    border-top: 1px solid #eee;
    position: absolute;
    bottom: 50px;
    margin: 0;
    height: 0;
}

.fm-picker-bottom {
    width: 100%;
    border: none;
    border-top: 1px solid #eee;
    position: absolute;
    bottom: 85px;
    margin: 0;
    height: 0;
}

.fm-picker-content-list {
    float: left;
    min-width: 30%;
    text-align: center;
    position: relative;
    z-index: 99;
}

.fm-picker-content-list .picker-cover {
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    z-index: 9999;
}
.fm-picker-content-list ul {
    position: relative;
    z-index: 99;
    -webkit-transition: all .3s ease;
    transition: all .3s ease;
    background: none;
}

.fm-picker-content-list ul.data_dragging{
    -webkit-transition: none;
    transition: none;
}

.fm-picker-content-list li {
    line-height: 35px;
    height: 35px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    font-size: 14px !important;
    position: relative;
    z-index: -9;
}

.fm-picker-content-list li.current {
    font-size: 16px !important;
    font-weight: bold;
}

.fm-picker-content-list li.node1 {
    font-size: 15px !important;
    opacity: .7;
}

.fm-picker-content-list li.node2 {
    font-size: 14px !important;
    opacity: .5;
}

.fm-picker-content-list li.node3 {
    font-size: 12px !important;
    opacity: .3;
}
</style>
