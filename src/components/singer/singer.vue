<template>
  <div class="singer">歌手</div>
</template>

<script type="text/ecmascript-6">
  import {getSingerList} from 'api/singer'
  import {ERR_OK} from 'api/config'

  const HOT_NAME = '热门'
  const HOT_SINGER_LEN = 10

  export default {
    data() {
      return {
        singerList: []
      }
    },
    created() {
      this._getSingerList()
    },
    methods: {
      _getSingerList() {
        getSingerList().then((res) => {
          if (res.code === ERR_OK) {
            this.singerList = this._normalizeSinger(res.data.list)
          }
        })
      },
      _normalizeSinger(list) {
        let map = {
          hot: {
            tilt: HOT_NAME,
            items: []
          }
        }
        list.forEach((item, index) => {
          if (index < HOT_SINGER_LEN) {
            map.hot.items.push({
              id: item.Fsinger_mid,
              name: item.Fsinger_name,
              avator: `http://y.gtimg.cn/music/photo_new/T001R150x150M000${item.Fsinger_mid}.jpg?max_age=2592000`
            })
          }
          const key = item.Findex
          if (!map[key]) {
            map[key] = {
              title: key,
              items: []
            }
            map[key].items.push({
              id: item.Fsinger_mid,
              name: item.Fsinger_name,
              avator: `http://y.gtimg.cn/music/photo_new/T001R150x150M000${item.Fsinger_mid}.jpg?max_age=2592000`
            })
          }
        })
        console.log(map)
      }
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .singer
    position: fixed
    width: 100%
    top: 88px
    bottom: 0
</style>
