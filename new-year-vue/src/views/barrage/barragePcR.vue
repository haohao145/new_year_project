<template>
  <div
    class="aaa barrage-bg1 barrages-drop dmbg barrages"
    :class="{barragebg1h:bg==1}"
    style="width: 100%;height: 100%;"
  >
    <div class="dmkk1">
      <div class="dmkkn">
        <div class="dmkknn">
          <vue-baberrage
            :isShow="barrageIsShow"
            :barrageList="barrageList"
            :loop="barrageLoop"
            :maxWordCount="maxWordCount"
            :throttleGap="throttleGap"
            :boxHeight="boxHeight"
            :messageHeight="messageHeight"
          ></vue-baberrage>
        </div>
      </div>
    </div>

    <!-- <img src="../../assets/img/peopleImg/renwu.jpg" alt /> -->
  </div>
</template>

<script>
//引入弹幕插件
import { MESSAGE_TYPE } from 'vue-baberrage'
let renwu = require('../../assets/img/peopleImg/renwu.jpg')
let txx = require('../../assets/img/systemImg/gstx.jpg')
//获取 ws 的请求地址
import serverConfig from '../../../public/serverConfig.json'
import utils from '../../utils/utils'
export default {
  // name: "prize",
  data() {
    return {
      msg: '开始弹幕吧',
      barrageIsShow: true, //是否显示
      currentId: 0,
      messageHeight: 14, //弹幕高度
      boxHeight: 570, //显示弹幕区域的高度
      barrageLoop: false, //是否循环
      maxWordCount: 1, //弹幕最大字数长度，超过则忽略
      throttleGap: 100, //弹幕之间的节流时间
      barrageList: [], //数据
      bg: 0,
      color: [
        'blue',
        'green',
        'red',
        'yellow',
        'zdy-huang',
        'blue',
        'green',
        'red',
        'yellow',
        'zdy-huang'
      ]
    }
  },
  created() {},
  mounted() {
    // 初始化WebSocket
    this.initWebSocket()

    this.addToList()

    //窗口关闭时 断开连接
    let _this = this
    window.onbeforeunload = function() {
      _this.websock.close()
    }
  },
  methods: {
    //初始化  WebSocket
    initWebSocket() {
      console.log('启动中')
      let wsurl = serverConfig.ws
      this.websock = new WebSocket(wsurl)
      this.websock.onopen = this.websocketonopen //连接成功
      this.websock.onmessage = this.websocketonmessage //广播成功
      this.websock.onerror = this.websocketonerror //连接断开，失败
      this.websock.onclose = this.websocketclose //连接关闭
    },
    //初始化weosocket
    websocketonopen() {
      console.log('连接成功')
    },
    //连接成功
    websocketonerror() {
      console.log('连接失败')
    },
    //连接失败
    websocketclose() {
      console.log('断开连接')
    },
    //发送数据
    websocketsend(data) {
      this.websock.send(JSON.stringify(data))
    },
    //监听消息
    websocketonmessage(data) {
      let _this = this
      //监听 服务器的通知
      let restu = JSON.parse(data.data)

      //换背景
      if (restu.type == 'bg') {
        this.bg = this.bg == 0 ? 1 : 0
      }
      // console.log("data:", restu.type);
      // console.log(restu);
      let sjs = Math.floor(Math.random() * 10)
      let sjss = sjs > 7 ? sjs : 7
      if (restu.type == 'barrage') {
        // if (restu.text == "开始卖酒") {
        //   this.barrageList.push({
        //     // id: "00",
        //     avatar: txx,
        //     msg:
        //       restu.data[0].staffName +
        //       "右边的第" +
        //       utils.randomNum(1, 12) +
        //       "位喝一杯！",
        //     time: sjss,
        //     type: MESSAGE_TYPE.NORMAL,
        //     barrageStyle: _this.color[Math.ceil(Math.random() * 10)]
        //   });
        //   return;
        // }
        let mc = restu.data[0].staffName
        if (restu.data[0].FamilyPeople == 1) {
          mc = restu.data[0].staffName + '家属-' + restu.data[0].Familyname
        }
        this.barrageList.push({
          // id: "00",
          avatar: serverConfig.imgpath + restu.data[0].imgpath,
          msg: mc + '：' + restu.text,
          time: sjss,
          type: MESSAGE_TYPE.NORMAL,
          barrageStyle: _this.color[Math.ceil(Math.random() * 10)]
        })
      }
    },
    // 弹幕配置
    addToList() {
      let _this = this
      let list = [
        {
          id: 1,
          avatar: renwu,
          msg: this.msg,
          time: 8,
          barrageStyle: _this.color[Math.ceil(Math.random() * 10)]
        }
        // {
        //   id: 2,
        //   avatar: renwu,
        //   msg: "测试弹幕的类可行",
        //   time: 110,
        //   barrageStyle: "red"
        // },
      ]
      list.forEach(v => {
        this.barrageList.push({
          id: v.id,
          avatar: v.avatar,
          msg: v.msg,
          time: v.time,
          type: MESSAGE_TYPE.NORMAL,
          barrageStyle: v.barrageStyle
        })
      })
    }
  }
}
</script>

<style lang="less" >
.barrage-bg1 {
  background: url(../../assets/img/systemImg/dmbg-r.jpg) no-repeat center/100%
    100%;
}
.barragebg1h {
  background: url(../../assets/img/systemImg/dmbg-rh.jpg) no-repeat center/100%
    100%;
}
.barrages-drop {
  .blue {
    border-radius: 100px;
    background: #e6ff75;
    color: #fff;
  }
  .green {
    border-radius: 100px;
    background: #75ffcd;
    color: #fff;
  }
  .red {
    border-radius: 100px;
    background: #d10d28;
    color: #fff;
  }
  .yellow {
    border-radius: 100px;
    background: #dfc795;
    color: #fff;
  }
  .zdy-huang {
    border-radius: 100px;
    color: #000;
    background: #fbb86a;
  }
  .baberrage-stage {
    position: absolute;
    width: 100%;
    height: 100%;
    overflow: hidden;
    top: 0;
    // margin-top: 130px;
    color: #fff;
  }
}
.dmkk1 {
  width: 1414px;
  height: 948px;
  background: url('../../assets/img/systemImg/dm-r.png') no-repeat center/ 100%;
  position: absolute;
  right: 0px;
  top: 50%;
  margin-top: -455px;
  // padding: 30px 0;
  box-sizing: border-box;
}
.dmkkn {
  width: 984px;
  height: 673px;
  position: absolute;
  left: 0;
  right: 0;
  top: -39px;
  bottom: 0;
  // padding: 30px 0;
  box-sizing: border-box;

  margin: auto;
  // position: relative;
  // margin-top: -40px;
}
.aaa {
}
.dmkknn {
  width: 100%;
  height: 100%;
  position: relative;
}
.barrages .baberrage-item .baberrage-avatar {
  width: 66px !important;
  height: 66px !important;
  left: -16px !important;
  top: -10px !important;
}
.baberrage-item .baberrage-avatar img {
  width: 100% !important;
  height: 100%;
}
.baberrage-item .baberrage-msg {
  padding-left: 20px !important;
}
.dmbg {
  // background: url();
}
</style>
