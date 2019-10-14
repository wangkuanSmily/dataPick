<template>
  <div class="fm-picker-content-list">
    <div class="picker-cover"   @touchstart="_onTouchStart('data', $event)"
         @mousedown="_onTouchStart('data', $event)"  @touchmove="_stopDef" @mousewheel="_stopDef">
    </div>
    <ul ref:data-list
        :class="{'data_dragging': dataState.dragging}"
        :style="{'transform' : 'translate3d(0,' + dataState.translateY + 'px, 0)'}">
      <li></li>
      <li></li>
      <li v-for="(item, index) in dataState.data"
          :key="index"
          :class="{
                                      'current':  Math.abs(index - dataState.index) == 0,
                                      'node1':  Math.abs(index - dataState.index) == 1,
                                      'node2':  Math.abs(index - dataState.index) == 2,
                                      'node3':  Math.abs(index - dataState.index) >= 3
                                  }"
      >{{item.name ? item.name : item}}
      </li>
      <li></li>
      <li></li>
      <li></li>
    </ul>
  </div>
</template>
<script>
    export default {
        data: function () {
            return {
                result: this.propsResult,
                target: '',
                dataState: {
                    data: null,
                    selectedId: null,
                    index: 0,
                    startPos: null,
                    translateY: 0,
                    startTranslateY: 0,
                    dragging: false
                },
                delta: 0,
                slideEls: null
            }
        },
        mounted () {
            this.render()
        },
        methods: {
            render() {
                let vm = this
                this.dataState.data = this.data
                for(let i = 0; i < vm.data.length; i++){
                    if(vm.result === vm.data[i] || (vm.result && vm.result.id && vm.result.id === vm.data[i].id)){
                        vm.dataState.index = i
                        vm.dataState.selectedId = vm.propsResult.id ? vm.propsResult.id : vm.propsResult
                        vm.dataState.translateY = i * 35 * (-1)
                    }
                }

            },
            _stopDef(e){
                e.preventDefault()
            },
            setPage() {
                let target = this.target;
                let thisData = this[target + 'State'];
                let clientHeight = this.slideEls[0]['clientHeight'];
                let total = thisData.data.length;
                let goPage = Math.round((thisData.translateY / clientHeight).toFixed(1));
                if (goPage > 0) {
                    goPage = 0;
                }
                goPage = total - 1 >= Math.abs(goPage) ? goPage : -(total - 1);
                let index = Math.abs(goPage);
                thisData.index = index;
                thisData.translateY = goPage * clientHeight;
                this.$emit('choose',thisData)
            },
            _getTouchPos(e) {
                return e.changedTouches ? e.changedTouches[0]['pageY'] : e['pageY'];
            },
            _getElem(e){
                return Array.from(e.currentTarget.nextElementSibling.children).slice(3, -3);
            },
            _onTouchStart(target, e){
                let thisData = this[target + 'State'];
                this.target = target;
                this.slideEls = this._getElem(e);
                this.delta = 0;
                thisData.startPos = this._getTouchPos(e);
                thisData.startTranslateY = thisData.translateY;
                thisData.dragging = true;
                document.addEventListener('touchmove', this._onTouchMove, false);
                document.addEventListener('touchend', this._onTouchEnd, false);
                document.addEventListener('mousemove', this._onTouchMove, false);
                document.addEventListener('mouseup', this._onTouchEnd, false);
            },
            _onTouchMove(e) {
                let target = this.target;
                let thisData = this[target + 'State'];
                this.delta = this._getTouchPos(e) - thisData.startPos;
                thisData.translateY = thisData.startTranslateY + this.delta;
                if (Math.abs(this.delta) > 0) {
                    e.preventDefault();
                }
            },
            _onTouchEnd() {
                let target = this.target;
                let thisData = this[target + 'State'];
                thisData.dragging = false;
                this.setPage();
                document.removeEventListener('touchmove', this._onTouchMove);
                document.removeEventListener('touchend', this._onTouchEnd);
                document.removeEventListener('mousemove', this._onTouchMove);
                document.removeEventListener('mouseup', this._onTouchEnd);
            },
        },
        watch: {
            data() {
                this.render()
                let last = this.data.length - 1
                if (this.dataState.index > last) {
                    this.dataState.index = last
                    this.dataState.selectedId = this.data[last].id
                    this.dataState.translateY = last * 35 * (-1)
                }
            },
            propsResult(val) {
                let vm = this
                for(let i = 0; i < vm.data.length; i++){
                    if(val.id == vm.data[i].id){
                        vm.dataState.index = i
                        vm.dataState.selectedId = val.id
                        vm.dataState.translateY = i * 35 * (-1)
                    }
                }
            }
        },
        props: {
            'propsResult': {
                type: Object,
                default: null
            },
            'data':{
                type: Array
            },
        }
    }
</script>
<style>


</style>
