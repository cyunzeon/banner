<template>
  <div class='index-wrap'>
    <div class="number-wrap" v-if="showPass">
      <h4>选择你的双色球号码</h4>
      <p class="qc">第{{qici}}期 周{{week}} {{hs}}开奖</p>
      <p class="tit">还可免费领取<span>{{notes}}</span>注</p>
      <div class="ball">
        <span v-for="item in chooseRed" :key="item">{{item}}</span>
        <span class="blue">{{chooseBlue[0]}}</span>
        <img src="../assets/images/reflash.png" alt="" @click="shakeAction">
      </div>
      <input type="number" placeholder="输入手机号码注册领取" oninput="if(value.length > 11)value = value.slice(0,11)"
        v-model="mobileNo">
      <!--showPass = false-->
      <div class="btns" v-if="mobileNo.length > 10" @click="checkRegit(mobileNo)">加入抢号</div>
      <div class="btn" v-else>加入抢号</div>
    </div>
    <div class="number-wraps" v-else>
      <h4>选择你的双色球号码</h4>
      <p class="qc">第{{qici}}期 周{{week}} {{hs}}开奖</p>
      <p class="tit">还可免费领取<span>{{notes}}</span>注</p>
      <div class="ball">
        <span v-for="item in chooseRed" :key="item">{{item}}</span>
        <span class="blue">{{chooseBlue[0]}}</span>
        <img src="../assets/images/reflash.png" alt="" @click="shakeAction">
      </div>
      <input type="number" placeholder="输入手机号码注册领取" oninput="if(value.length > 11)value = value.slice(0,11)"
        v-model="mobileNo">
      <!-- <input type="password" placeholder="请输入密码" v-model="password"> -->
      <input type="number" placeholder="请输入验证码" v-model="codes" oninput="if(value.length > 6)value = value.slice(0,6)">
      <div class="btns" @click="regMob">加入抢号</div>
      <div class="yzm" @click="sendImgYzm" id='sendYzmbtn'>获取验证码</div>
    </div>

    <div class="people-wrap">
      <h4>正在抢号</h4>
      <div class="box">当前<span v-for="items in peopleNum" :key="items.index">{{items}}</span>人</div>
    </div>
    <div class="tips-wrap">
      <h4>活动规则</h4>
      <p> 1、本活动限彩中宝新注册用户参与
      </p>
      <p> 2、未注册用户参与本活动，将视为自愿注册彩中宝
      </p>
      <p> 3、只有在本活动页面参与才可以享受免费双色球优惠
      </p>
      <p> 4、本活动限时限量领取，先到先得，请尽快领取
      </p>
      <p> 5、同一IP，设备，手机号只能领取一次，如领取不成功，请切换4G网络尝试
      </p>
      <p> 6、严禁通过作弊手段刷取活动奖励，一经发现，将收回奖励并封号处理
      </p>
      <p> 7、活动所有解释权归彩中宝所有
      </p>
    </div>

    <div class="shadow" v-show="showYzm">
      <div class="yzm">
        <img src="../assets/images/close.png" alt="" @click="showYzm = false" class="close">
        <input type="text" placeholder="请输入图形验证码" oninput="if(value.length > 4)value = value.slice(0,4)" v-model="txm">
        <img :src="yzmImgUrl" alt="" class="ymg" @click="changeYzm">
        <div class="btn" @click="yzmBtn">确定</div>
      </div>
    </div>
    <div class="shadow" v-show="showLogin">
      <div class="toast-box">
        <p>已为你快捷生成账号</p>
        <p>登录激活后立即生效</p>
        <img src="../assets/images/close2.png" alt="" @click="showLogin = false">
        <div class="btn" @click='btnAction'>确定</div>
      </div>
    </div>
    <div class="shadow" v-show="showApp">
      <div class="toast-box">
        <p>没抢到？</p>
        <p>更多优惠活动赶紧去app看看</p>
        <img src="../assets/images/close2.png" alt="" @click="showApp = false">
        <div class="btn" @click='btnAction'>打开APP，去看看</div>
      </div>
    </div>

  </div>
</template>

<script>
  import {
    login,
    mobregzym,
    checkRegit,
    mobregByGift,
    ssq
  } from '@/request/api';
  import {
    get
  } from '@/request/http';

  import {
    Toast
  } from 'mint-ui';
  import $ from 'jquery';
  export default {
    data() {
      return {
        chooseRed: [],
        chooseBlue: [],
        redList: [],
        blueList: [],
        timeList: [],
        redNumMax: 33,
        redNumSize: 6,
        blueNumMax: 16,
        blueNumSize: 1,
        peopleNum: '',
        notes: '',
        mobileNo: '',
        password: '',
        codes: '',
        showYzm: false,
        showLogin: false,
        showApp: false,
        showPass: true,
        yzmImgUrl: '',
        txm: '',
        sendYzmbtn: true,
        num: 60,
        timer: null,
        qici: '',
        hs: '',
        week: '',
        comeFrom: '',
        cFrom: '',
        ballList: []
      };
    },
    created() {
      //this.countNum();
      this.shakeAction();
      this.notesNum();
      this.changeYzm();
      this.getSsq();
      let ua = navigator.userAgent.toLowerCase();
      //ios终端
      let isiOS = !!ua.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
      if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
        //ios
        this.comeFrom = 136478;
        this.cFrom = 'ios';
      } else {
        //android
        this.comeFrom = 136479;
        this.cFrom = 'android';
      }

      //console.log(this.chooseRed.join(',') + '|' + this.chooseBlue.join(""))
    },
    mounted() {

    },
    methods: {
      countNum() {
        this.peopleNum = parseInt(this.peopleNum) + Math.floor(Math.random() * (100 - 1)) + 1;
        this.peopleNum = this.peopleNum.toString();
        this.notes = parseInt(this.notes) + Math.floor(Math.random() * (100 - 1)) + 1;
        this.notes = this.notes.toString();
        setTimeout(this.countNum, 1000);
      },
      getSsq() {
        var url = "/cpdata/game/01/s.json?rnd=" + Math.random();
        get(url).then(res => {
          this.qici = res.period.row[0].pid;
          this.hs = res.period.row[0].at.slice(10, 16);
          var arys1 = new Array();
          arys1 = res.period.row[0].at.slice(0, 11).split('-'); //日期为输入日期，格式为 2013-3-10
          var ssdate = new Date(arys1[0], parseInt(arys1[1] - 1), arys1[2]);
          this.week = String(ssdate.getDay()).replace("0", "日").replace("1", "一").replace("2", "二").replace("3",
            "三").replace("4", "四").replace("5", "五").replace("6", "六"); //就是你要的星期几
        })
      },
      sendImgYzm() {
        const t = this;
        const umob = t.mobileNo,
          upwd = t.password;
        if (!t.sendYzmbtn) {
          return false
        }
        if (umob == '') {
          Toast("请输入手机号码");
          return false;
        }
        if (!(/^1[345678]\d{9}$/.test(umob))) {
          Toast("手机号码有误，请重填");
          return false;
        } else {
          t.changeYzm();
          t.showYzm = true;
        }
        /*if (!(/^[a-zA-Z0-9]{6,20}$/.test(upwd))) {
          Toast('请设置您的登陆密码,6-20个字符,只能含有数字和字母');
          return false;
        } else {
          t.changeYzm();
          t.showYzm = true;
        }*/
      },
      yzmBtn() {
        const t = this;
        const txm = t.txm,
          mobileNo = t.mobileNo;
        let num = t.num,
          timer = t.timer;
        if (txm.length != 4) {
          Toast('请输入正确的验证码')
          return false;
        }
        mobregzym({
          "flag": 1,
          'yzm': txm,
          'comeFrom': t.comeFrom,
          'cfrom': t.cFrom,
          'mobileNo': mobileNo
        }).then(res => {
          const code = res.data.Resp.code;
          const desc = res.data.Resp.desc;
          if (code == 0) {
            t.sendYzmbtn = false;
            t.txm = '';
            t.showYzm = false;
            $('#sendYzmbtn').css({
              'backgroundColor': '#e0e0e0',
              'color': '#666'
            })
            timer = setInterval(function () {
              num--;
              $('#sendYzmbtn').html(num + 's重新发送');
              if (num == 0) {
                t.sendYzmbtn = true;
                clearInterval(timer);
                $('#sendYzmbtn').html('获取验证码');
                $('#sendYzmbtn').css({
                  'backgroundColor': '#fa8080',
                  'color': '#fff'
                });
              }
            }, 1000);
          } else {
            Toast(desc);
          }
        })
        // mobregzym({
        //   "flag": 1,
        //   'yzm': this.txm,
        //   'mobileNo': this.mobileNo
        // }).then(res => {
        //     console.log(res)
        //     if(res.data.Resp.code == 0) {
        //         this.showYzm = false;
        //         this.showApp = true;
        //     }
        // })
      },
      regMob() {
        const t = this;
        const umob = t.mobileNo,
          upwd = t.password,
          yzm = t.codes;
        if (umob == '') {
          Toast("请输入手机号码");
          return false;
        }
        if (!(/^1[345678]\d{9}$/.test(umob))) {
          Toast("手机号码有误，请重填");
          return false;
        }
        /*if (!(/^[a-zA-Z0-9]{6,20}$/.test(upwd))) {
          Toast('请设置您的登陆密码,6-20个字符,只能含有数字和字母');
          return false;
        }*/
        mobregByGift({
          'mobileNo': umob,
          'yzm': yzm,
          'pwd': upwd,
          'comeFrom': this.comeFrom,
          'codes': this.ballList,
          'gid': '01',
          'cfrom': this.cFrom,
          'fid': 'free'
        }).then(v => {
          const code = v.data.Resp.code,
            desc = v.data.Resp.desc;
          if (code == 0) {
            //t.showPass = true;
            t.showLogin = true;
          } else {
            Toast(desc);
          }
        })
      },
      //随机数
      getRandom(max) {
        return Math.floor(Math.random() * max) + 1;
      },
      //排序
      sortNum(arr) {
        for (let i = 0; i < arr.length; i++) {
          for (let j = 1; j < arr.length - i; j++) {
            if (arr[i] > arr[i + j]) {
              let temp = arr[i]
              arr[i] = arr[i + j]
              arr[i + j] = temp
            }
          }
        }
        return arr
      },
      //随机生成球
      createNumArr(numArr, numSize, numMax) {
        // 清空已有数组
        numArr.splice(0, numArr.length)
        while (numArr.length < numSize) {
          let num = this.getRandom(numMax)
          // 当生成的号码与原有号码不重复时，才加入到数组中
          if (numArr.indexOf(num) === -1) {
            numArr.push(num)
          }
        }
        return this.sortNum(numArr)
      },
      //生成红球
      createRedNumArr() {
        this.createNumArr(this.chooseRed, this.redNumSize, this.redNumMax)
      },
      //生成蓝球
      createBlueNumArr() {
        this.createNumArr(this.chooseBlue, this.blueNumSize, this.blueNumMax)
      },
      //随机生成按钮
      shakeAction() {
        this.redList = [];
        this.blueList = [];
        this.ballList = [];
        this.createRedNumArr();
        this.createBlueNumArr();
        this.chooseRed.forEach(item => {
          item = item < 10 ? '0' + item : item;
          this.redList.push(item)
        });
        this.chooseBlue.forEach(items => {
          items = items < 10 ? '0' + items : items;
          this.blueList.push(items)
        });
        this.ballList = this.redList.join(',') + '|' + this.blueList.join(",") + ':1:1';
        console.log(this.ballList)
      },
      notesNum() {
        let d = new Date();
        let hh = d.getHours();
        //let curTimestamp = parseInt(new Date().getTime() / 1000); //当前时间戳
        let curTimestamp =  (new Date()).valueOf(); //当前时间戳
        let timestampDiff = parseInt((curTimestamp % (1000 * 60 * 60)) / (1000 * 60)) + hh * 60; // 参数时间戳与当前时间戳相差秒数
       console.log(timestampDiff)
        console.log(parseInt((curTimestamp % (1000 * 60 * 60)) / (1000 * 60)))
        this.peopleNum = 10000 + timestampDiff*25;
        this.peopleNum = this.peopleNum.toString();
        this.notes = 10000 - timestampDiff*6;
        //this.peopleNum = Math.floor(Math.random() * (50000 - 10000)) + 10000;
        //this.peopleNum = this.peopleNum.toString();
        //this.notes = Math.floor(Math.random() * (10000 - 1000)) + 1000;
        //this.notes = this.notes.toString();
      },
      btnAction() {
        let ua = navigator.userAgent.toLowerCase();
        //ios终端
        let isiOS = !!ua.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
        if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
          //ios
          window.location = 'https://apps.apple.com/cn/app/%E6%8E%8C%E4%B8%8A%E5%AE%9D-%E4%BE%BF%E6%8D%B7%E5%B0%8F%E5%8A%A9%E6%89%8B/id1474061339'
        } else {
          //android
          window.location = 'http://down.688cai.com/bdcaizbaz/bdcaizbaz.apk'
        }
      },
      changeYzm() {
        this.txm = '';
        this.yzmImgUrl = "http://h.5988cai.com/rand.phpx?rnd=" + Math.random();
      },
      checkRegit(val) {
        checkRegit({
          'mobileNo': val,
          'comeFrom': this.comeFrom,
          'gid': '01',
          'cfrom': this.cFrom
        }).then((v) => {
          let code = v.data.Resp.falg;
          if (code == 1) { //已注册
            this.showApp = true;
          } else {
            this.showPass = false;
          }
        })
      }
    },
  };

</script>

<style lang='scss' scoped>
  .index-wrap {
    width: 100%;
    background: url('../assets/images/bg.png') no-repeat;
    background-size: cover;
    padding-bottom: 50px;
    padding-top: 720px;

    .number-wrap {
      width: 690px;
      height: 548px;
      background: url('../assets/images/1.png') no-repeat;
      background-size: cover;
      /* position: relative;
      left: 0;
      right: 0; */
      margin: 0 auto;
      display: flex;
      flex-direction: column;
      align-items: center;

      h4 {
        font-size: 36px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
      }

      .qc {
        font-size: 24px;
        font-family: PingFang SC;
        font-weight: 400;
        color: rgba(255, 255, 255, 1);
      }

      .tit {
        font-size: 24px;
        font-family: PingFang SC;
        font-weight: 400;
        color: rgba(51, 51, 51, 1);
        line-height: 140px;

        span {
          font-size: 30px;
          font-family: PingFang SC;
          font-weight: 400;
          color: rgba(213, 80, 73, 1);
          padding: 0 14px;
        }
      }

      .ball {
        display: flex;
        align-items: center;

        span {
          display: block;
          width: 68px;
          height: 68px;
          background: rgba(230, 86, 79, 1);
          border-radius: 50%;
          font-size: 30px;
          font-family: PingFang SC;
          font-weight: 500;
          color: rgba(255, 255, 255, 1);
          line-height: 68px;
          text-align: center;
          margin: 0 4px;
        }

        .blue {
          background: #4177F5;
        }

        img {
          width: 42px;
          height: 42px;
          margin-left: 12px;
        }
      }

      input {
        width: 594px;
        height: 56px;
        background: rgba(251, 234, 211, 1);
        border: 0;
        -webkit-appearance: none;
        border: 2px solid rgba(244, 202, 159, 1);
        border-radius: 43px;
        font-size: 34px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(51, 51, 51, 1);
        line-height: 56px;
        margin: 20px 0;
        text-align: center;
        padding: 15px 0;
      }

      input::-webkit-input-placeholder {
        color: rgba(244, 202, 159, 1);
        text-align: center;
      }

      .btn {
        width: 594px;
        height: 86px;
        background: rgba(245, 195, 182, 1);
        border-radius: 43px;
        font-size: 34px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
        line-height: 86px;
        text-align: center;
      }

      .btns {
        width: 594px;
        height: 86px;
        background: #D55049;
        border-radius: 43px;
        font-size: 34px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
        line-height: 86px;
        text-align: center;
      }
    }

    .number-wraps {
      width: 690px;
      height: 746px;
      background: url('../assets/images/login.png') no-repeat;
      background-size: cover;
      /* position: relative;
      left: 0;
      right: 0;
      margin: auto; */
      margin: 0 auto;
      display: flex;
      flex-direction: column;
      align-items: center;

      h4 {
        font-size: 36px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
        padding-top: 30px;
      }

      .qc {
        font-size: 24px;
        font-family: PingFang SC;
        font-weight: 400;
        color: rgba(255, 255, 255, 1);
      }

      .tit {
        font-size: 24px;
        font-family: PingFang SC;
        font-weight: 400;
        color: rgba(51, 51, 51, 1);
        padding: 30px 0 40px 0;

        span {
          font-size: 30px;
          font-family: PingFang SC;
          font-weight: 400;
          color: rgba(213, 80, 73, 1);
          padding: 0 14px;
        }
      }

      .ball {
        display: flex;
        align-items: center;

        span {
          display: block;
          width: 68px;
          height: 68px;
          background: rgba(230, 86, 79, 1);
          border-radius: 50%;
          font-size: 30px;
          font-family: PingFang SC;
          font-weight: 500;
          color: rgba(255, 255, 255, 1);
          line-height: 68px;
          text-align: center;
          margin: 0 4px;
        }

        .blue {
          background: #4177F5;
        }

        img {
          width: 42px;
          height: 42px;
          margin-left: 12px;
        }
      }

      input {
        width: 594px;
        height: 56px;
        background: rgba(251, 234, 211, 1);
        border: 0;
        -webkit-appearance: none;
        border: 2px solid rgba(244, 202, 159, 1);
        border-radius: 43px;
        line-height: 56px;
        margin: 18px 0;
        text-indent: 70px;
        font-size: 34px;
        font-family: PingFang SC;
        font-weight: 400;
        color: rgba(51, 51, 51, 1);
        padding: 15px 0;
      }

      input::-webkit-input-placeholder {
        color: rgba(244, 202, 159, 1);
      }

      .btn {
        width: 594px;
        height: 86px;
        background: rgba(245, 195, 182, 1);
        border-radius: 43px;
        font-size: 34px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
        line-height: 86px;
        text-align: center;
      }

      .btns {
        width: 594px;
        height: 86px;
        background: #D55049;
        border-radius: 43px;
        font-size: 34px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
        line-height: 86px;
        text-align: center;
        margin-top: 40px;
      }

      .yzm {
        width: 160px;
        height: 50px;
        background: rgba(214, 80, 74, 1);
        border-radius: 25px;
        font-size: 24px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
        line-height: 50px;
        text-align: center;
        position: absolute;
        top: 1186px;
        right: 96px;
      }
    }

    .people-wrap {
      width: 690px;
      height: 192px;
      background: url('../assets/images/2.png') no-repeat;
      background-size: cover;
      position: relative;
      left: 0;
      right: 0;
      margin: 30px auto;

      h4 {
        font-size: 36px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(255, 255, 255, 1);
        display: flex;
        flex-direction: column;
        align-items: center;
        line-height: 70px;
      }

      .box {
        width: 100%;
        font-size: 34px;
        font-family: PingFang SC;
        font-weight: 400;
        color: rgba(51, 51, 51, 1);
        display: flex;
        align-items: center;
        justify-content: center;
        margin: 26px 0;

        span {
          display: block;
          width: 60px;
          height: 60px;
          background: rgba(251, 234, 211, 1);
          border: 2px solid rgba(244, 202, 159, 1);
          font-size: 34px;
          font-family: PingFang SC;
          font-weight: 400;
          color: rgba(230, 86, 79, 1);
          line-height: 60px;
          text-align: center;
          margin: 0 10px;
        }
      }
    }

    .tips-wrap {
      width: 690px;
      height: 600px;
      background: rgba(255, 252, 248, 1);
      border-radius: 50px;
      padding: 44px 42px;
      box-sizing: border-box;
      position: relative;
      left: 0;
      right: 0;
      margin: auto;

      h4 {
        text-align: center;
        font-size: 36px;
        font-family: PingFang SC;
        font-weight: 500;
        color: rgba(51, 51, 51, 1);
        padding-bottom: 34px;
      }

      p {
        font-size: 24px;
        font-family: PingFang SC;
        font-weight: 400;
        color: rgba(51, 51, 51, 1);
        line-height: 48px;
      }
    }

    .shadow {
      position: fixed;
      top: 0;
      left: 0;
      bottom: 0;
      right: 0;
      background: rgba(0, 0, 0, 0.5);

      .yzm {
        width: 590px;
        height: 324px;
        background: rgba(255, 255, 255, 1);
        border-radius: 20px;
        position: fixed;
        bottom: 10%;
        left: 80px;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        align-items: center;

        .close {
          width: 46px;
          height: 46px;
          position: relative;
          left: 260px;
        }

        .ymg {
          width: 145px;
          height: 70px;
          position: absolute;
          top: 110px;
          right: 100px;
        }

        input {
          width: 460px;
          height: 80px;
          background: rgba(246, 245, 249, 1);
          border: 0;
          -webkit-appearance: none;
          border: 1px solid rgba(153, 153, 153, 1);
          border-radius: 50px;
          line-height: 80px;
          font-size: 34px;
          font-family: PingFang SC;
          font-weight: 400;
          color: rgba(51, 51, 51, 1);
          text-indent: 34px;
          padding: 10px 0;
        }

        input::-webkit-input-placeholder {
          font-size: 26px;
          font-family: PingFang SC;
          font-weight: 400;
          color: rgba(222, 173, 123, 1);
        }



        .btn {
          width: 210px;
          height: 78px;
          background: rgba(213, 80, 73, 1);
          border-radius: 39px;
          font-size: 34px;
          font-family: PingFang SC;
          font-weight: 500;
          color: rgba(255, 255, 255, 1);
          line-height: 78px;
          text-align: center;
        }
      }

      .toast-box {
        width: 610px;
        height: 506px;
        background: url('../assets/images/toast.png') no-repeat;
        background-size: cover;
        position: fixed;
        top: 512px;
        left: 80px;
        padding-top: 70px;
        box-sizing: border-box;

        img {
          width: 50px;
          height: 50px;
          position: absolute;
          top: -90px;
          right: 60px;
        }

        p {
          font-size: 30px;
          font-family: PingFang SC;
          font-weight: 400;
          color: rgba(213, 80, 73, 1);
          line-height: 60px;
          text-align: center;
        }

        .btn {
          width: 480px;
          height: 76px;
          background: linear-gradient(0deg, rgba(213, 80, 73, 1) 0%, rgba(181, 54, 50, 1) 100%);
          border-radius: 38px;
          font-size: 35px;
          font-family: Source Han Sans CN;
          font-weight: 400;
          color: rgba(255, 255, 255, 1);
          line-height: 76px;
          text-align: center;
          position: absolute;
          top: 350px;
          left: 66px;
        }
      }

    }
  }

  @media screen and (min-width:1200px) {
    .index-wrap {
      .number-wraps {
        .yzm {
          right: 1060px;
        }
      }

      .shadow {
        .yzm {
          left: 1020px;
        }
      }
    }
    
  }

</style>
