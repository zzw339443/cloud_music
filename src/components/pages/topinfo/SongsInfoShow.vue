<template>
  <div v-if="$Check(detail)" class="detail-show container clearfix" :class="{ small: small }">

      <div class="top clearfix">
        <div class="left">
          <img class="cover eff-shadow" :src="detail['coverImgUrl']" alt="">
        </div>
        <div class="center">
          <div class="primary">{{ detail['name'] }}</div>
          <div class="user-detail">
            <avatar class="avatar" size="40px"
                    :avatar-url="detail['creator']['avatarUrl']"
                    :uid="detail['creator']['userId']"/>
            <user-name font-size="13px" class="user-name" :uid="detail['creator']['userId']">
              {{detail['creator']['nickname']}}
            </user-name>
            <span class="createTime">
            {{detail['createTime'] | dateTimeFormat('yyyy-MM-dd') }} 创建
          </span>
          </div>
          <div class="option">
            <span class="icon el-icon-video-play"></span>
            <span :class="{ disabled: isMySongs, active: detail['subscribed'] }"
                  class="icon el-icon-star-off"
                  @click="onCollect"></span>
            <span class="icon el-icon-link"></span>
          </div>
        </div>
        <div class="info">
          <div class="song-count">
            歌曲数：{{detail['trackCount'] | logogram}}
          </div>
          <div class="play-count">
            播放：{{detail['playCount'] | logogram}}
          </div>
        </div>
      </div>

      <div class="desc">
        <div class="tags">
          标签：{{detail['tags'] | resloveTags }}
        </div>
        <text-fold class="synopsis"
                  :text="detail['description'] | resloveSynopsis"/>

      </div>

  </div>
</template>

<script>

  import { str_empty } from "@/utils/utils";
  import BusTypes from "@/utils/bus/types"

  import Avatar from "@/components/content/label/Avatar";
  import UserName from "@/components/content/label/UserName";
  import TextFold from '@/components/common/TextFold'
  import {playlist_sub} from "@/network/behavior";

  export default {
    name: "DetailShow",
    components: {UserName, Avatar, TextFold},
    props: {
      detail: { type: Object,  default: () => {} },
      small:  { type: Boolean, default: false }
    },

    methods: {
      onCollect() {
        if (!this.$store.state.isLogin) {
          this.$bus.$emit(BusTypes.PLACE_LOGIN)
          return
        }

        playlist_sub(this.detail['id'], !this.detail['subscribed']).then(res => {
          let message = this.detail['subscribed']? '已取消收藏': '收藏成功'
          this.detail['subscribed'] = !this.detail['subscribed']
          this.$notify.success({
            title: '提示',
            message
          })
        })
      }
    },

    computed: {
      isMySongs() {
        return this.$store.state.profile.UID == this.detail['creator']['userId']
      }
    },

    filters: {
      resloveTags(value) {
        return value.length === 0 ? '还没有添加标签' : value.join(' / ')
      },

      resloveSynopsis(value) {
        return str_empty(value)
          ? `简介：没有谁的生活会一直完美，但无论什么时候，都要看着前方，满怀希望就会所向披靡。(默认简介)`
          : '简介：' + value
      }
    }
  }
</script>

<style scoped>

  /*small样式*/

  .small .left .cover {
    width: 150px;
    margin-top: 20px;
    border-radius: 10px;
  }

  .small .center {
    margin-top: 10px;
  }

  .small .center .primary {
    font-size: 17px;
  }

  .small .center > * {
    margin-top: 15px;
  }


  /*普通样式*/

  .detail-show.container {
    padding-bottom: 20px;
    position: relative;
  }

  .left, .center {
    float: left;
  }

  .left .cover {
    width: 200px;
    border-radius: 10px;
  }

  .center {
    margin-left: 30px;
  }


  .center .primary {
    font-size: 20px;
    font-weight: bold;

    margin-top: 15px;
  }

  .user-detail {
    height: 45px;
    display: flex;
    align-items: center;

    margin-top: 20px;
  }

  .user-detail .avatar {
    border: 1px solid #e0e0e0;
  }

  .user-detail .user-name {
    font-size: 13px;
    margin-left: 8px;
  }

  .user-detail .createTime {
    font-size: 11px;
    margin-left: 25px;
    color: #000;
  }

  .desc {
    font-size: 11px;
    /*font-weight: bold;*/
    margin-top: 20px;
  }

  .option {
    margin-top: 25px;
  }

  .option .icon:not(:first-child) {
    margin-left: 15px;
  }

  .option .icon {
    font-size: 20px;
    padding: 8px;
    border-radius: 50%;
    box-shadow: 2px 2px 10px 0 rgba(0, 0, 0, 0.2);

    transition: all 0.3s;
    cursor: pointer;
  }

  .option .icon.disabled {
    opacity: 0.5;
  }

  .option .icon.active {
    color: #d35400
  }

  .info {
    position: absolute;
    top: 140px;
    right: 0;
    font-size: 11px;
    font-weight: bold;
    color: #bdbdbd;
    width: 90px;
  }
</style>
