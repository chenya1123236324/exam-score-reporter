<template>
  <view>
    <view class="fixed report-btn">
      <van-button color="#7232dd" class="padding-btn" block round @tap="showPopup"
                  type="danger">提交问题</van-button>
    </view>
    <van-popup :show="show" position="bottom" @close="onClose">
      <view>
        <van-field class="feedback" :value="content" @change="changeValue"
                   type="textarea"  :autosize="{ minHeight: 50 }" auto-focus
                   placeholder="请输入问题反馈或需求提交" />
        <view class="issue-type">
          <view class="issue-type-title">问题类型： </view>
          <van-radio-group :value="issueType" @change="changeIssueType"
                           class="issue-type-radios">
            <van-radio name="bug">BUG</van-radio>
            <van-radio name="req">需求</van-radio>
          </van-radio-group>
        </view>
        <van-button class="padding-btn" block round @tap="submit" type="primary">
          提交
        </van-button>
      </view>
    </van-popup>
    <van-toast id="van-toast" />

  </view>
</template>

<script>
import api from 'utils/api.js'
import Toast from 'wxcomponents/vant/toast/toast'

export default {
  data() {
    return {
      show: false,
      content: ' ',
      issueType: 'bug',
    }
  },

  components: {},
  props: {
    columns: {
      type: Array,
      default: () => ([]),
    },
    value: {
      type: String,
      default: '',
    },
  },
  options: {
    styleIsolation: 'apply-shared',
  },

  methods: {
    showPopup() {
      this.show = true
      setTimeout(() => {
        this.content = ''
      }, 300)
    },

    onClose() {
      this.show = false
    },

    changeValue(evt) {
      this.content = evt.detail
    },
    changeIssueType(evt) {
      console.log(evt)
      this.issueType = evt.detail
    },
    submit() {
      if (!this.content) {
        Toast('内容不能为空')
        return
      }
      uni.showLoading({
        title: '提交中...',
      })
      const systemInfo = wx.getSystemInfoSync()
      api.wxCloudCallFunction('add', {
        collectionName: 'report',
        content: this.content,
        issueType: this.issueType,
        systemInfo,
      }).then(res => {
        Toast('添加成功')
        console.log(res)
        this.$emit('change', event.detail)
        this.onClose()
      }).finally(() => {
        uni.hideLoading()
      })
    },

  },
}
</script>
<style lang="scss">
  .feedback{
    min-height: 200upx;
  }
  .issue-type{
    display: flex;
    align-items: center;
    padding: 0 30upx;
    &-title{
      font-size: 36upx;
      line-height: 50upx;
      color: #333;
      margin-right: 30upx;
    }
  }
  .issue-type-radios{
    display: flex;
    justify-content: space-around;
    height: 120upx;
    align-items: center;
    flex-grow: 1;
  }
</style>
