<template>
    <div style="padding-bottom:150rpx;">
        <div class="ipts">
            <span class="ititle">报修单号:</span>
            <span class="ript">{{origin.danhao}}</span>
        </div>
        <div class="ipts">
            <span class="ititle">状态</span>
            <span
                :class="[{  gray  : detail.state=='0' },{  yellow  : detail.state=='1' },{  green  : detail.state=='2' },{  red  : detail.state=='3' },{  blue  : detail.state==='4' }, 'ript']"> {{detail.state == 0 ? "待处理" : (detail.state == 1 ? "维修中" : (detail.state == 2 ? "已完成" : (detail.state == 3 ? "已中止" : "反馈中")))}}</span>
        </div>
        <div v-if="detail.fktype&&detail.state ==4" class="ipts">
            <span class="ititle">反馈理由:</span>
            <span class="ript riptcontent">{{detail.fktype}}</span>
        </div>
        <div v-if="detail.fkbeizhu&&detail.state ==4" class="ipts">
            <span class="ititle" style="float: none;">备注:</span>
            <span class="  fkbeizhu">{{detail.fkbeizhu }}</span>
        </div>
        <div v-if="origin.weixiugong" class="ipts">
            <span class="ititle">维修工:</span>
            <span class="ript">{{origin.weixiugong}}</span>
        </div>
        <div v-if="origin.arrivetime" class="ipts">
            <span class="ititle">到场时间:</span>
            <span class="ript">{{origin.arrivetime}}</span>
        </div>
        <div class="ipts">
            <span class="ititle">报修人姓名:</span>
            <span class="ript">{{origin.name}}</span>
        </div>
        <div class="ipts">
            <span class="ititle">手机号:</span>
            <span class="ript phone" @click='makeacall' :data-cell="origin.phone">{{origin.phone}}</span>
        </div>
        <div class="ipts">
            <span class="ititle">报修类型:</span>
            <span class="ript ">{{origin.type}}</span>
        </div>
        <div class="ipts">
            <span class="ititle">报修内容:</span>
            <span class="ript riptcontent">{{origin.content}}</span>
        </div>
        <div class="ipts">
            <span class="ititle imgs ">现场图片:</span>
            <div style="margin-left:45rpx;width:auto;">
                <div class="imgbox" v-for="(item,index) in origin.imgsUrl" :key="index">
                    <img v-if="item" :src="item" :mode="'widthFix'" @click='preview(index)' class="slt" alt="缩略图">

                    <span v-else
                          style="display:block;width: 100%;height: 100%;text-align: center;line-height:184rpx;background: rgba(0,0,0,0.14)">暂无图片</span>
                </div>
            </div>
        </div>
        <!--<div class = "ipts" v-if = "detail.state!=='0'">
            <span class = "ititle imgs ">维修图片:</span>
            <div style = "margin-left:45rpx;width:auto;">
                <div class = "imgbox" v-for = "(item,index) in MaintenancePics" :key = "index">
                    <img :src = "item" :mode = "'widthFix'" @click = 'preview2(index)' class = "slt" alt = "缩略图">
                </div>
            </div>
        </div>-->
        <div class="ipts">
            <span class="ititle">车站：</span>
            <div class="loca">
                <i-icon style="position:relative;top:-4rpx;" type="coordinates_fill" size="26" color="#2d8cf0"
                        class="usericon"/>
                <span class="spans  ">{{origin.station}}</span>
            </div>
        </div>
        <div class="ipts">
            <span class="ititle" style="float: none;">详细位置：</span>
            <span class="address addressworker">{{origin.address }}</span>
        </div>

        <div class="ipts">
            <span class="ititle">台单号：</span>
            <span class="ript">{{origin.taidanhao?origin.taidanhao:'空'}}</span>
        </div>

        <div class="ipts" v-if="detail.state!=='0'&&detail.state!=='4'"
             style="padding: 10rpx;border: 2rpx dashed  rgba(255,209,119,0.96);box-sizing: border-box;width: 92%;margin-top: 20rpx;">
            <span>维修详情：</span>
            <div class="weixiug" v-for="(item,index) in  Repairs" :key="item.RPID">
                <div class="lists">
                    <span class="listsleft">维修工：</span>
                    <span class="listsright">{{item.UserName}}</span>
                </div>
                <div class="lists">
                    <span class="listsleft">电话：</span>
                    <span class="listsright phone" @click='makeacall' :data-cell="item.Mobile">{{item.Mobile}}</span>
                </div>
                <div class="lists">
                    <span class="listsleft">状态：</span>
                    <span class="listsright">{{item.RepairStatus}}</span>
                </div>
                <!--                <div class="lsits" v-if="workimgshow(item.RepairPics)"> &lt;!&ndash;v-if="item.imgsShow"&ndash;&gt;-->
                <div class="lsits" v-if=" item.imgsShow "> <!--v-if="item.imgsShow"-->
                    <span class="ititle imgs ">现场图片:</span>
                    <div style="margin-left:45rpx;width:auto;">
                        <div class="imgbox" v-for="(listitem,listindex) in item.RepairPics"
                             :key="item.RPID+listindex">
                            <img :src="listitem" :mode="'widthFix'"
                                 @click='workeRpreview(listindex,item.RepairPics)'
                                 class="slt" alt="缩略图">
                        </div>
                    </div>
                    <div class="lists">
                        <span class="listsleft">维修备注：</span>
                        <span class="listsright" style="height: auto;text-align: justify">{{item.RepairContent}}</span>
                    </div>
                </div>
                <i-button type="info" v-if="item.RepairStatus =='维修中'&&UID==item.UID&&detail.state =='1'"
                          :data-index="index" :data-uid="item.UID" size="small" style="display: block;margin-top: 60rpx"
                          @click=
                              "repairend">
                    确认维修结束
                </i-button>
            </div>
        </div>

        <!-- <span class="bxlist" v-if="ratealready&& detail.state!=='3'"><span class="spans inspan">满意度:</span> {{ratetxt}}
                 <van-rate count="3" size="20" :value="ratecode"
                           style="width: 50%;display: inline-block;position: relative;top: 14rpx;"
                 /></span>
 -->


        <div class="ipts" v-if="OrderType =='反馈单'">
            <span class="ititle" style="float: none;color: red"> <i-icon type="warning_fill"/> 该订单已经成功反馈 </span>
        </div>
        <i-row :class="'buttons'" v-if="peopletype=='gz'&&role==2&&OrderType!=='反馈单'">
            <i-col v-if="detail.state=='0'" :span="16">
                <i-button type="primary" @click="jiedan">接 单</i-button>
            </i-col>
            <i-col v-else-if="detail.state=='1'" :span="16">
                <i-button type="success" @click="finish">确认完成</i-button>
            </i-col>
            <i-col :span="8" v-if="detail.state=='0'||detail.state=='1' ">
                <i-button type="warning" @click="fankui">反 馈</i-button>
            </i-col>
        </i-row>
        <view class="paidan" v-if="paidanShow">
            <view class="paidancard">
                <img src="/static/images/close.png" class="cardclose" @click="paidanToggle">
                <span class="cardtitle">派单</span>
                <span class="cardline">
                    <span class="linetitel">*维修人员</span>
                    <span class="linedetail" @click.stop="selectPeople">{{peoples}} > </span>
                </span>
                <span class="cardline" style="margin-bottom:75rpx;">
                    <span class="linetitel">*到场时间</span>
                    <span class="linedetail" @click.stop.click="dateTimePick">{{dateSelected}} > </span>
                </span>
                <view class="sure">
                    <i-button type="primary" @click='paidan'>确认派单</i-button>
                </view>
            </view>
            <van-popup :show="datepickershow" position="bottom">
                <van-datetime-picker :type="'datetime'" :data-value="currentDate" :value="peoplecurrent"
                                     v-model="currentDate" :min-date="minDate" @confirm="confirm" @cancel="cancel"/>
            </van-popup>
            <van-popup :show="peoplepickershow" position="bottom">
                <view class="check">
                    <span class="ckitem ckitem1" @click="peosltcancel">取消</span>
                    <span class="ckitem ckitem2" @click="peosltsure">确定</span>
                </view>
                <scroll-view scroll-y="true" :style="{ height: '460rpx'}">
                    <i-panel>
                        <i-checkbox-group :current="peoplecurrent" :data-set="peoplecurrent"
                                          @change="handleFruitChange">
                            <i-checkbox v-for=" (item,index) in houxuanren" :position="'right'" :key="item.id"
                                        :data-sid="item.id" :value="item.name">
                            </i-checkbox>
                        </i-checkbox-group>
                    </i-panel>
                </scroll-view>
            </van-popup>
        </view>
        <view class="fankui" v-if="fankuiShow" @touchmove.stop="">
            <view class="fankuicard">
                <img src="/static/images/close.png" class="cardclose" @click="fankuiToggle">
                <span class="cardtitle">反馈</span>
                <span class="fklytxt">*请选择反馈理由</span>
                <view class="section">
                    <view class="reasons">
                        <picker @change="bindPickerChange" :value="index" :range="array">
                            <view class="picker">
                                {{array[index]?array[index] +" > ":'请选择理由'}}
                            </view>
                        </picker>
                        <!--<picker @change = "bindPickerChange2" :value = "index2" :range = "array2">-->
                        <!--<view class = "picker2">-->
                        <!--{{array2[index2]?array2[index2]:'请选择理由'}}-->
                        <!--</view>-->
                        <!--</picker>-->
                    </view>
                </view>
                <span class="fklytxt">备注(选填)</span>
                <textarea v-if="fankuiShow" class="fklyDesc" @touchmove.prevent="" v-model="fklyDesc" type="textarea"
                          maxlength="200" autofocus placeholder="  输入备注内容（200字内）"></textarea>
                <!-- <span class = "fklytxt">录音汇报</span>
                 <button class = "fkbtns" @longpress = "start" @touchmove = "handleTouchMove" @touchend = "stop">
                     {{luyinwenzi}}
                 </button>
                 <button v-if = "playVoiceBtnShow" class = "fkbtns voiceplay" @tap = 'play'>
                     <img src = "/static/images/voice.png" class = "voice">
                     <span> {{time}}秒</span>
                 </button>
                 <img v-if = "playVoiceBtnShow" src = "/static/images/shanchu.png" class = "deleteVoice" @click.stop = "deleteVoice">-->
                <view class="fkbtn">
                    <i-button type="primary" @click.stop="quedingfankui">确认反馈</i-button>
                </view>
            </view>
        </view>
        <view class="finish" v-if="finishShow">
            <view class="finishcard">
                <img src="/static/images/close.png" class="cardclose" @click="finishToggle">
                <span class="finishend">维修结束</span>
                <div class="infos">


                    <van-radio-group :value="radio" @change="onChange"
                                     style="display: block;overflow: hidden;width: 570rpx">
                        <van-radio name="1" style="float: left;margin-left: 11%;margin-right: 10%;"
                                   checked-color="#19be6b">已完成
                        </van-radio>
                        <van-radio name="2" style="float: left;" checked-color="red">不能完成</van-radio>
                    </van-radio-group>


                    <!--
                                        <span class="linetitel">维修状态：</span>
                                        <view class="section" style="text-align: right">
                                            <picker @change="bindPickerChange2" :value="index3" :range="finishState">
                                                {{finishState[index3]}} >
                                            </picker>
                                        </view>-->


                </div>

                <div class="uoloadimgs" :data-upid="index" @click="uploadImg" v-for="(item,index) in upload"
                     :key="index">
                    <span>+</span>
                    <span class="tips">上传图片</span>
                    <div v-if="item.show" class="imgbox">
                        <img :src="item.imgurl" :mode="'widthFix'" @click.stop="SelectPreview(index)"
                             class="uploadedimg">
                    </div>
                    <img v-if="item.show" :data-upid="index" src="/static/images/delete.png" @click.stop='deleteImg'
                         class="delete">
                </div>
                <span class="finishtips">如果维修完成，请上传现场反馈图片。</span>
                <input type="text" placeholder="备注信息" :value="beizhu" v-model="beizhu"
                       style="border: 0.5px solid #cacaca;display: block;width: 92%;height: 34px;margin-bottom: 17px;padding: 2px;margin: 0 auto;"/>
                <i-button type="success" @click.stop="workerEndcertain" size="small"
                          style="position: relative;left: 0;top: 30rpx;">
                    确认结束
                </i-button>
            </view>
        </view>
        <i-toast id="toast"/>
        <i-modal :visible="dingdanfinish" @ok="dingdanfinishShow" @cancel="dingdanfinishHide">
            <view>确认完成订单吗？</view>
        </i-modal>
    </div>
</template>
<script>
    import {$Toast} from '../../../static/iview/base/index'
    // import {getCurrentPageUrlWithArgs} from '@/utils/index';
    export default {
        data() {
            return {
                radio: '0',
                dingdanfinish: false,
                UID: '',
                OID: '',//訂單ID
                workerUID: '',
                role: 2, //维修工是1 工长是2
                index: 0,
                index2: 0,
                index3: 0,
                array: ['修不了', '需要协助'], //“修不了”“需要协助”
                array2: [],    // 时间选择器
                minHour: 10,
                maxHour: 20,
                minDate: new Date().getTime(),
                maxDate: new Date(2019, 10, 1).getTime(),
                currentDate: new Date().getTime(),
                // 时间选择器
                //身份选择
                peopletype: 'gz', // wxg  gz  维修工 和 工区工长
                //身份选择
                //确认完成
                upload: [
                    {imgurl: '', show: false},
                    {imgurl: '', show: false},
                    {imgurl: '', show: false},
                ],
                //确认完成
                imgsUrl: [],
                imgsId: [],
                beizhu: '',
                detail: {
                    state: 2,
                    banzu: '',
                    gongzhang: '',
                    gzcell: '',
                    weixiugong: '',
                    wxgcell: '',
                    arrivetime: '',
                    jubao: '',
                    location: '',
                    fktype: '',
                    fkbeizhu: "",
                    origin: {
                        danhao: '',
                        time: "",
                        name: "",
                        phone: "",
                        type: '',
                        content: [],
                        imgsUrl: [],
                        station: "",
                        address: "",
                        taidanhao: ''
                    },
                },
                origin: {
                    danhao: '',
                    time: "",
                    name: "",
                    phone: "",
                    type: '',
                    content: [],
                    imgsUrl: [],
                    station: "",
                    address: "",
                    taidanhao: '',
                    weixiugong: ''
                },
                selectIndex: '',
                judgeShow: false,
                reasonShow: false,
                badReason: "",
                paidanShow: false,
                fankuiShow: false,
                finishShow: false,
                datepickershow: false,
                peoplepickershow: false,
                dateSelected: "请选择",
                list: ['a', 'b', 'c'],
                result: ['a', 'b'],
                houxuanren: [],
                peoplecurrent: [],
                peoplecurrentindex: [],
                position: 'right',
                animal: 'xx',
                checked: false,
                disabled: false,
                fklyDesc: "",
                sendLock: false,
                startY: '',
                luyinwenzi: "长按添加录音",
                playVoiceBtnShow: false,
                timeinterval: '',
                time: 0,
                oid: '',
                daochangshijian: '',
                //现场图片
                MaintenancePics: [],
                finishState: ['已完成', '不能完成'],
                repairIndex: 0,
                Repairs: [],
                OrderType: '',
                clickstate: 0,
                initurl: ''
            }
        },
        computed: {
            judgement: function () {
                console.log(this.detail.state);
                return (this.detail.state == 2 || this.detail.state == 3) ? true : false
            },
            peoples: function () {
                if (this.peoplecurrent.length > 0) {
                    return this.peoplecurrent
                } else {
                    return "请选择 "
                }
            },
        },
        methods: {
            onChange(event) {
                this.radio = event.mp.detail
                // console.log(event);

            },
            dingdanfinishShow() {
                console.log('完成关闭订单，并且更新订单')
                this.finishCertain()
            },
            dingdanfinishHide() {
                this.dingdanfinish = false
            },
            workimgshow: function (data) {
                console.log(data);
                console.log("ssssssssss")
                var arrtemp = data.join('')
                if (arrtemp.length > 0) {
                    return true//show
                } else {
                    return false// hide
                }

            },
            makeacall(e) {
                let wx = mpvue
                let number = e.mp.currentTarget.dataset.cell
                console.log(number)
                wx.makePhoneCall({
                    phoneNumber: number //仅为示例，并非真实的电话号码
                })
            },
            preview: function (key) {
                wx.previewImage({
                    current: this.origin.imgsUrl[key], // 当前显示图片的http链接
                    urls: this.origin.imgsUrl // 需要预览的图片http链接列表
                })
            },
            preview2: function (key) {
                wx.previewImage({
                    current: this.MaintenancePics[key], // 当前显示图片的http链接
                    urls: this.MaintenancePics // 需要预览的图片http链接列表
                })
            },
            workeRpreview(key, data) {
                wx.previewImage({
                    current: data[key], // 当前显示图片的http链接
                    urls: data // 需要预览的图片http链接列表
                })
            },
            jiedan() {
                this.paidanShow = true
                console.log("jiedan");
            },
            fankui() {
                console.log("fankui");
                this.fankuiShow = true
            },
            quedingfankui() {
                console.log("fankui");
                // this.fankuiShow = true
                var _this = this
                var wx = mpvue
                if (_this.array[_this.index] == "请选择反馈类型") {
                    $Toast({
                        content: '请选择反馈类型',
                        type: 'warning'
                    });
                } else {
                    wx.request({
                        url: 'https://hd.xmountguan.com/railway/order.aspx?func=update_order&oid=' + this.oid + '&order_status=5' + '&uid=' + wx.getStorageSync("UID") + "&maintenancePics=" + ' ' + '&process=' + _this.fklyDesc + "&feedbackreason=" + _this.array[_this.index],
                        success(res) {
                            console.log(res);
                            if (res.data.success = 'success') {
                                _this.fankuiShow = false
                            }
                            _this.detail.state = 4

                            wx.setStorageSync('stateChange', '5');
                            _this.refresh()


                            _this.detail.fkbeizhu = _this.fklyDesc
                            _this.detail.fktype = _this.array[_this.index]


                        },
                        fail(w) {
                            console.log(w);
                        }
                    })
                }


                /* if (_this.fklyDesc == '') {   //
                     $Toast({
                         content: '请完善反馈描述',
                         type: 'warning'
                     });
                 } else {

                 }*/


            },
            repairend(e) {
                console.log(e);
                console.log("repairend");
                this.finishShow = true;
                this.workerUID = e.mp.currentTarget.dataset.uid
                this.repairIndex = e.mp.currentTarget.dataset.index
            },
            finish() {
                console.log("finsihed");
                // this.finishShow = true
                this.dingdanfinish = true
            },
            selectPeople: function () {
                console.log("选择人员")
                this.peoplepickershow = true
            },
            dateTimePick: function () {
                console.log("dateTimePick")
                this.datepickershow = true
            },
            confirm(e) {
                console.log(e.mp.detail);
                this.currentDate = e.mp.detail
                var timeSelected = this.formatDate(e.mp.detail)
                this.detail.arrivetime = timeSelected;
                this.dateSelected = timeSelected
                console.log(timeSelected)
                this.datepickershow = !this.datepickershow
                this.daochangshijian = timeSelected
            },
            cancel: function () {
                console.log("pickerclose");
                this.datepickershow = !this.datepickershow
            },
            formatDate: function (timestamp) {
                var date = new Date(timestamp);
                var Y = date.getFullYear() + '-';
                var M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
                var D = (date.getDate() < 10 ? '0' + (date.getDate()) : date.getDate()) + ' ';
                var h = (date.getHours() < 10 ? '0' + (date.getHours()) : date.getHours()) + ':';
                var m = (date.getMinutes() < 10 ? '0' + (date.getMinutes()) : date.getMinutes()) + ':';
                var s = (date.getSeconds() < 10 ? '0' + (date.getSeconds()) : date.getSeconds());
                return Y + M + D + h + m + s;
            },
            formatter(type, value) {
                if (type === 'year') {
                    return `${value}年`;
                } else if (type === 'month') {
                    return `${value}月`;
                }
                return value;
            },

            handleFruitChange: function (e) {
                var detailvalue = e.mp.detail.value
                const index = this.peoplecurrent.indexOf(detailvalue);
                index === -1 ? this.peoplecurrent.push(detailvalue) : this.peoplecurrent.splice(index, 1);
                this.peoplecurrent = this.peoplecurrent
                /*     console.log(e.mp.currentTarget.dataset.set);
                 console.log(this.peoplecurrent);*/
                console.log(this.peoplecurrent)
                var strings = this.peoplecurrent;
                this.detail.weixiugong = strings.join(',')
                var peoplecurrentindex = [];
                for (var i in strings) {
                    var indexfind = this.houxuanren.filter((e) => e.name == strings[i])[0];
                    console.log(indexfind);
                    peoplecurrentindex[i] = indexfind.id;
                }
                this.peoplecurrentindex = peoplecurrentindex
            },
            peosltcancel() {
                this.peoplepickershow = !this.peoplepickershow
            },
            peosltsure() {
                this.peoplepickershow = !this.peoplepickershow
            },
            paidanToggle() {
                this.paidanShow = !this.paidanShow
            },
            finishToggle() {
                this.finishShow = !this.finishShow
            },
            fankuiToggle() {
                this.fankuiShow = !this.fankuiShow
            },
            bindPickerChange(e) {
                console.log('picker发送选择改变，携带值为', e.mp.detail.value)
                this.index = e.mp.detail.value
            },
            bindPickerChange2: function (e) {
                console.log('picker发送选择改变，携带值为', e.mp.detail.value)
                this.index3 = e.mp.detail.value

            },
            play: function () {
                const innerAudioContext = wx.createInnerAudioContext()
                //播放声音文件
                innerAudioContext.autoplay = true
                innerAudioContext.src = this.tempFilePath,
                    innerAudioContext.onPlay(() => {
                        console.log('开始播放')
                    })
                innerAudioContext.onError((res) => {
                    console.log(res.errMsg)
                    console.log(res.errCode)
                })
            },
            start: function (e) {
                console.log(e.y)
                this.startY = e.y
                $Toast({
                    content: '录音中，上滑取消',
                    image: '/static/images/record.png',
                    duration: 0,
                });
                //开始录音
                console.log("开始录音")
                const options = {
                    duration: 10000, //指定录音的时长，单位 ms
                    sampleRate: 16000, //采样率
                    numberOfChannels: 1, //录音通道数
                    encodeBitRate: 96000, //编码码率
                    format: 'mp3', //音频格式，有效值 aac/mp3
                    frameSize: 50, //指定帧大小，单位 KB
                }
                this.luyinwenzi = "松开 结束"
                const recorderManager = wx.getRecorderManager()
                //开始录音
                recorderManager.start(options);
                recorderManager.onStart(() => {
                    console.log('recorder start')
                });
                //错误回调
                recorderManager.onError((res) => {
                    console.log(res);
                })
            },
            stop: function () {
                //结束录音
                console.log("结束录音")
                $Toast.hide();
                const recorderManager = wx.getRecorderManager()
                recorderManager.stop();
                recorderManager.onStop((res) => {
                    console.log(res);
                    if (res.duration < 1000) {
                        $Toast({
                            content: '录音时间太短',
                            image: '/static/images/back.png',
                            duration: 3,
                        });
                    } else {
                        this.time = Math.ceil(res.duration / 1000)
                        this.tempFilePath = res.tempFilePath;
                        console.log('停止录音', res.tempFilePath)
                        const {tempFilePath} = res;
                        this.playVoiceBtnShow = true
                    }
                })
            },
            handleTouchMove(e) {
                console.log(e.touches[e.touches.length - 1].clientY - this.startY);
                //touchmove时触发一
                var moveLenght = e.touches[e.touches.length - 1].clientY - this.startY; //移动距离
                if (Math.abs(moveLenght) > 400) {
                    $Toast.hide();
                    $Toast({
                        content: '松开手指取消',
                        image: '/static/images/back.png',
                        duration: 3,
                    });
                    this.sendLock = true; //触发了上滑取消发送，上锁
                    this.stop()
                } else if (Math.abs(moveLenght) > 200) {
                    $Toast.hide();
                    $Toast({
                        content: '松开手指取消',
                        image: '/static/images/back.png',
                        duration: 0,
                    });
                    this.sendLock = false; //上划距离不足，依然可以发送，不上锁
                }
            },
            deleteVoice() {
                this.playVoiceBtnShow = false;
                this.luyinwenzi = "长按添加录音"
            },
            SelectPreview: function (k) {
                var wx = mpvue
                var urls = []
                urls.push(this.imgsUrl[k])
                wx.previewImage({
                    current: urls[0], // 当前显示图片的http链接
                    urls: urls // 需要预览的图片http链接列表
                })
            },
            deleteImg(e) {
                var _this = this
                var indexOfLoad = e.mp.currentTarget.dataset.upid
                _this.upload[indexOfLoad].show = false
                _this.upload[indexOfLoad].imgurl = ''
                _this.imgsUrl[indexOfLoad] = ''
                _this.imgsId[indexOfLoad] = ''
            },
            uploadImg(e) {
                var _this = this
                var indexOfLoad = e.mp.currentTarget.dataset.upid
                let wx = mpvue
                let i = 0;					// 多图上传时使用到的index
                let that = this;
                let max = that.maxImg;		//最大选择数
                let upLength;						//图片数组长度
                let imgFilePaths;				//图片的本地临时文件路径列表
                wx.chooseImage({
                    count: max || 1,           //一次最多可以选择的图片张数
                    sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
                    sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
                    success: function (res) {
                        // tempFilePath可以作为img标签的src属性显示图片
                        const tempFilePaths = res.tempFilePaths
                        // console.log(tempFilePaths);
                        _this.imgsUrl[indexOfLoad] = tempFilePaths[0]
                        _this.upload[indexOfLoad].imgurl = tempFilePaths
                        _this.upload[indexOfLoad].show = true
                        /**
                         * 上传完成后把文件上传到服务器
                         */
                        _this.fileUpload(tempFilePaths, indexOfLoad)
                        // $Toast({
                        //     content: '开始上传文件',
                        //     type: 'warning'
                        // });
                    },
                    fail: function () {
                        console.log('fail');
                        $Toast({
                            content: '已取消',
                            type: 'warning'
                        });
                    },
                    complete: function () {
                        console.log('完成同步上传');
                    }
                })
            },
            fileUpload: function (tempFilePaths, index) {
                console.log("待上传 ：" + tempFilePaths);
                var wx = mpvue
                var that = this;
                var _this = this
                wx.uploadFile({
                    url: "https://hd.xmountguan.com/railway/upload_single_pic.aspx",//url地址， //app.ai_api.File.file
                    filePath: tempFilePaths.toString(),//要上传文件资源的路径 String类型
                    name: 'uploadimg',//按个人情况修改，文件对应的 key,开发者在服务器端通过这个 key 可以获取到文件二进制内容，(后台接口规定的关于图片的请求参数)
                    header: {
                        "Content-Type": "multipart/form-data"//记得设置
                    },
                    formData: {
                        //和服务器约定的token, 一般也可以放在header中
                        // 'session_token': wx.getStorageSync('session_token')
                    },
                    success: function (res) {
                        //当调用uploadFile成功之后，再次调用后台修改的操作，这样才真正做了修改头像
                        if (res.statusCode = 200) {
                            var data = JSON.parse(res.data)
                            // var statusCode = res.statusCode
                            // console.log("返回值1" + data);
                            // console.log("返回值2" + statusCode)
                            //这里调用后台的修改操作， tempFilePaths[0],是上面uploadFile上传成功，然后赋值到修改这里。
                            console.log(data);
                            console.log("文件路径" + data.imgurl);
                            _this.imgsUrl[index] = data.imgurl
                            _this.upload[index].imgurl = data.imgurl
                            _this.imgsId[index] = data.imgID
                            console.log("待提交文件", _this.upload)
                            // $Toast({
                            //     content: '上传成功',
                            //     type: 'success',
                            //     duration: 2,
                            // });
                        }
                    },
                    fail(e) {
                        console.log(e);
                        $Toast({
                            content: '网络错误，请稍后重试',
                            type: 'warning'
                        });
                    }
                })
            },
            finishCertain() {
                var _this = this;
                console.log("确认完成");


                var arrtemp = []
                var imgsidforload = ''
                for (var item of this.imgsId) {
                    if (!(item == "" || item == undefined || item == null || typeof (item) == "undefined")) {
                        arrtemp.push(item)
                    }
                }
                imgsidforload = arrtemp.join(',')
                console.log(imgsidforload);

                wx.request({
                    url: 'https://hd.xmountguan.com/railway/order.aspx?func=update_order&oid=' + this.oid + '&uid=' + wx.getStorageSync("UID") + "&order_status=3" + '&maintenancePics=' + imgsidforload + '&process=' + this.beizhu,
                    success(res) {
                        console.log(res.data)
                        if (res.data.success) {
                            $Toast({
                                content: '操作成功',
                                type: 'success',
                                duration: 2,
                            });
                            _this.detail.state = 2
                            _this.finishShow = false;
                            _this.dingdanfinish = false;
                            _this.refresh()
                            wx.setStorageSync('stateChange', '2');
                            _this.refresh()
                        } else {
                            $Toast({
                                content: '操作失败',
                                type: 'warning'
                            });
                            _this.refresh()
                        }
                    }
                })
            },
            workerEndcertain() {
                var _this = this;
                console.log("确认完成");


                var arrtemp = []
                var imgsidforload = ''
                for (var item of this.imgsId) {
                    if (!(item == "" || item == undefined || item == null || typeof (item) == "undefined")) {
                        arrtemp.push(item)
                    }
                }
                imgsidforload = arrtemp.join(',')

                console.log(_this.beizhu);
                console.log(imgsidforload);
                if (_this.radio == 0) {
                    $Toast({
                        content: '请选择维修状态',
                        type: 'warning'
                    });
                } else {
                    wx.request({
                        url: 'https://hd.xmountguan.com/railway/order.aspx?func=update_repair&oid=' + _this.OID + '&uid=' + _this.workerUID + "&repair_status=" + (parseInt(_this.radio) + 1) + '&repair_pics=' + imgsidforload + '&repair_content=' + _this.beizhu,
                        success(res) {
                            console.log(res.data)
                            _this.finishShow = false;
                            if (res.data.success) {
                                $Toast({
                                    content: '操作成功',
                                    type: 'success',
                                    duration: 2,
                                });
                                _this.Repairs[_this.repairIndex].RepairStatus = '维修完毕'
                                _this.refresh()
                            } else {
                                $Toast({
                                    content: '操作失败',
                                    type: 'warning'
                                });
                            }
                        }
                    })
                }

                /* if (imgsidforload !== "" && _this.beizhu !== "") {

                 } else {
                     console.log("确认完成");
                     $Toast({
                         content: '请完善信息',
                         type: 'warning'
                     });
                 }*/
            },
            //确认派单
            paidan() {
                var _this = this
                console.log(this.peoplecurrentindex)
                console.log(this.daochangshijian)
                if (this.peoplecurrentindex.length < 1) {
                    $Toast({
                        content: '请选择维修人员',
                        type: 'warning'
                    });
                } else if (this.daochangshijian == "") {
                    $Toast({
                        content: '请选择到场时间',
                        type: 'warning'
                    });
                } else {
                    wx.request({
                        url: 'https://hd.xmountguan.com/railway/order.aspx?func=get_accept_order&oid=' + this.oid + '&uid=' + wx.getStorageSync("UID") + "&repair_uid=" + this.peoplecurrentindex.join(',') + '&assigntime=' + this.daochangshijian,
                        success(res) {
                            console.log(res.data)
                            if (res.data.success) {
                                $Toast({
                                    content: '派单成功',
                                    type: 'success',
                                    duration: 2,
                                });
                                _this.detail.state = 1
                                _this.paidanShow = false
                                wx.setStorageSync('stateChange', '1');
                                _this.refresh()
                                setTimeout(() => {
                                    wx.redirectTo({
                                        url: '../indexswiper/main?frompaidan=yes'
                                    })
                                })
                            } else {
                                $Toast({
                                    content: '操作失败',
                                    type: 'warning'
                                });
                                _this.refresh()
                            }
                        }
                    })
                }
            },
            refresh() {
                var _this = this
                this.oid = this.$root.$mp.query.oid;
                console.log(this.oid)
                console.log(this.$root.$mp.appOptions)
                console.log(this.$root.$mp.query)
                wx.request({
                    url: this.initurl + '&oid=' + this.oid,
                    success(res) {
                        var databack = res.data
                        var statusText = databack.OrderStatus
                        console.log("statusText" + statusText);
                        var statuscode = ''
                        if (statusText == "待处理") {
                            _this.detail.state = "0"
                        } else if (statusText == "维修中") {
                            _this.detail.state = "1"
                        } else if (statusText == "已完成") {
                            _this.detail.state = "2"
                        } else if (statusText == "已中止") {
                            _this.detail.state = "3"
                        } else if (statusText == "反馈中") {
                            _this.detail.state = "4"
                        }
                        var json = {
                            statuscode: statuscode,
                            danhao: databack.SerialNo,
                            time: databack.CreateTime,
                            name: databack.ReportUser,
                            phone: databack.Mobile,
                            type: databack.MaintenanceType,
                            content: databack.MaintenanceContentValue,
                            imgsUrl: databack.Pictures,
                            station: databack.Station,
                            address: databack.DetailLocation,
                            taidanhao: databack.TaidanNo,
                            weixiugong: databack.RepairMan,
                            arrivetime: databack.Repairs.length > 0 ? databack.Repairs[0].AssignTime : '',
                        }

                        for (var i = 0; i < databack.Repairs.length; i++) {
                            console.log("維修詳情");
                            console.log(databack.Repairs[i]);
                            console.log(databack.Repairs[i].RepairPics);
                            var state = false
                            var arrtemp = databack.Repairs[i].RepairPics.join('')
                            if (arrtemp.length > 0) {
                                state = true//show
                            } else {
                                state = false// hide
                            }

                            databack.Repairs[i].imgsShow = state


                        }
                        _this.detail.fkbeizhu = databack.Process
                        _this.detail.fktype = databack.FeedbackReason

                        _this.Repairs = databack.Repairs;
                        _this.origin = json
                        _this.OrderType = databack.OrderType;
                        _this.MaintenancePics = databack.MaintenancePics
                    }
                })
                //维修工集合
                wx.request({
                    url: 'https://hd.xmountguan.com/railway/user.aspx?func=get_repairman_list&uid=' + wx.getStorageSync('UID'),
                    success(res) {
                        console.log(res.data)
                        var databack = res.data
                        _this.houxuanren = []
                        for (var i = 0; i < databack.length; i++) {
                            var s = {
                                id: databack[i].uid,
                                name: databack[i].username,
                            }
                            _this.houxuanren.push(s)
                        }
                    }
                })
            },
            comshow() {
                console.log(this.detail.state)
                if (this.detail.state == "0") {
                    return false
                } else {
                    return true
                }
            }
        },
        mounted: function (options) {
            this.role = wx.getStorageSync('Role');
            var _this = this
            this.oid = this.$root.$mp.query.oid;
            this.clickstate = this.$root.$mp.query.state;
            console.log(this.clickstate);
            this.initurl = "https://hd.xmountguan.com/railway/order.aspx?func=get_pending_detail"
            if (this.clickstate) {
                this.initurl = "https://hd.xmountguan.com/railway/order.aspx?func=get_order_detail"
            }
            this.UID = wx.getStorageSync("UID")
            this.OID = _this.oid
            console.log(this.oid)
            console.log(this.$root.$mp.appOptions)
            console.log(this.$root.$mp.query)
            wx.request({
                url: this.initurl + '&oid=' + this.oid,
                success(res) {
                    var databack = res.data;
                    console.log(databack);
                    var statusText = databack.OrderStatus
                    console.log("statusText" + statusText);
                    var statuscode = ''
                    if (statusText == "待处理") {
                        _this.detail.state = "0"
                    } else if (statusText == "维修中") {
                        _this.detail.state = "1"
                    } else if (statusText == "已完成") {
                        _this.detail.state = "2"
                    } else if (statusText == "已中止") {
                        _this.detail.state = "3"
                    } else if (statusText == "反馈中") {
                        _this.detail.state = "4"
                    }
                    var json = {
                        statuscode: statuscode,
                        danhao: databack.SerialNo,
                        time: databack.CreateTime,
                        name: databack.ReportUser,
                        phone: databack.Mobile,
                        type: databack.MaintenanceType,
                        content: databack.MaintenanceContentValue,
                        imgsUrl: databack.Pictures,
                        station: databack.Station,
                        address: databack.DetailLocation,
                        taidanhao: databack.TaidanNo,
                        weixiugong: databack.RepairMan,
                        arrivetime: databack.Repairs.length > 0 ? databack.Repairs[0].AssignTime : '',
                    }
                    _this.detail.fkbeizhu = databack.Process
                    _this.detail.fktype = databack.FeedbackReason


                    _this.origin = json;
                    _this.OrderType = databack.OrderType;
                    _this.MaintenancePics = databack.MaintenancePics;


                    for (var i = 0; i < databack.Repairs.length; i++) {
                        console.log("維修詳情");
                        console.log(databack.Repairs[i]);
                        console.log(databack.Repairs[i].RepairPics);
                        var state = false
                        var arrtemp = databack.Repairs[i].RepairPics.join('')
                        if (arrtemp.length > 0) {
                            state = true//show
                        } else {
                            state = false// hide
                        }

                        databack.Repairs[i].imgsShow = state


                    }
                    _this.Repairs = databack.Repairs;

                }
            })
            //维修工集合
            wx.request({
                url: 'https://hd.xmountguan.com/railway/user.aspx?func=get_repairman_list&uid=' + wx.getStorageSync('UID'),
                success(res) {
                    console.log(res.data)
                    var databack = res.data
                    _this.houxuanren = []
                    for (var i = 0; i < databack.length; i++) {
                        var name = "（工长）"
                        var s = {
                            id: databack[i].uid,
                            name: databack[i].username,
                        }
                        if (databack[i].role == 2) {
                            s.name = s.name + name
                        }
                        _this.houxuanren.push(s)
                    }
                }
            })
            //反馈理由
            wx.request({
                url: 'https://hd.xmountguan.com/railway/m.aspx?func=get_reason_temple_list',
                success(res) {
                    console.log(res.data)
                    var databack = res.data
                    _this.array = []
                    _this.array.push('请选择反馈类型')
                    for (var i = 0; i < databack.length; i++) {
                        _this.array.push(databack[i].ReasonTemple)
                    }
                }
            })
        },
        onload() {
            Object.assign(this.$data, this.$options.data())
        },
        onUnload() {
            console.log('onUnload', this)
            Object.assign(this.$data, this.$options.data())
        },


    }
</script>
<style>
    .graym, .bggray {
        color: gray;

    }


    .yellow, .bgyellow {
        color: #ff8c2e;
    }

    .green, .bggreen {
        color: green;
    }

    .red, .bgred {
        color: #ed283c;
    }
    .blue {
        color: rgb(0, 184, 255);
    }

    .ipts {
        width: 740rpx;
        min-height: 76rpx;
        line-height: 76rpx;
        font-size: 28rpx;
        display: block;
        margin: 0 auto;
        color: #3f4147;
        border-bottom: 1rpx solid rgba(222, 222, 222, 0.67);
    }

    .ipts:nth-child(even) {
        background: rgba(246, 255, 213, 0.07);
    }

    .ititle {
        display: block;
        float: left;
        padding-left: 10rpx;
    }

    .loca {
        float: right;
    }

    .imgs {
        float: none;
    }

    .ript {
        display: block;
        height: 70rpx;
        line-height: 70rpx;
        float: right;
        text-align: right;
        padding-right: 10rpx;
        /*background: #ededed;*/
        width: 525rpx;
    }

    .riptcontent {
        display: block;
        height: 56rpx;
        line-height: 56rpx;
        float: right;
        text-align: right;
        padding-right: 10rpx;
        width: auto;
        border: 1rpx solid rgba(63, 65, 71, 0.31);
        padding: 0 10rpx;
        border-radius: 10rpx;
        vertical-align: middle;
        margin-top: 10rpx;
    }

    .fkreason {
        float: right;
        border: 1rpx solid #1c2438;
        padding: 0 10rpx;
        height: 50rpx;
        line-height: 50rpx;
        margin-top: 18rpx;
        border-radius: 10rpx;
    }

    .phone {
        color: #2d8cf0;
    }

    .active .gou {
        display: inline;
    }

    .detailbox {
        width: 100%;
        padding: 20rpx;
        box-sizing: border-box;
        -webkit-box-sizing: border-box;
    }

    .xqtxt {
        font-weight: bold;
    }

    .detail {
        width: 100%;
        min-height: 600rpx;
        border: 1rpx solid rgba(128, 128, 128, 0.37);
        margin-top: 10rpx;
        padding: 10rpx;
        box-sizing: border-box;
        box-shadow: 0 0 10rpx rgba(92, 92, 92, 0.36);
    }

    .bxlist {
        width: 100%;
        display: block;
        min-height: 50rpx;
        line-height: 50rpx;
        font-size: 28rpx;
        text-align: justify;
        padding-left: 10rpx;
        box-sizing: border-box;
    }

    .spans,
    .neirong {
        display: inline-block;
    }

    .neirong {
        border: 1rpx solid #b2b8c5;
        border-radius: 10rpx;
        margin: 0 10rpx 10rpx 10rpx;
        box-sizing: border-box;
        padding: 0 10rpx;
        font-size: 24rpx;
    }

    .inspan {
        margin-right: 20rpx;
    }

    .imgbox {
        display: inline-block;
        width: 184rpx;
        height: 184rpx;
        background: #c6c6c6;
        margin: 0 20rpx 0 0;
        position: relative;
        overflow: hidden;
    }

    .slt {
        position: absolute;
        width: 100%;
        height: auto;
        display: block;
        left: 0;
        top: 50%;
        transform: translateY(-50%);
        -webkit-transform: translateY(-50%);
    }

    .judge {
        margin-bottom: 150rpx;
    }

    .addressworker,
    .fkbeizhu {
        display: block;
        float: none;
        background: #f7f4f4;
        width: 91%;
        margin: 0 auto;
        margin-bottom: 21rpx;
        line-height: 40rpx;
        padding: 10rpx;
        text-align: left;
    }

    .paidan,
    .fankui,
    .finish {
        width: 100vw;
        height: 100vh;
        position: fixed;
        left: 0;
        top: 0;
        z-index: 1000;
        background: rgba(128, 128, 128, 0.78);
    }

    .paidancard {
        width: 75%;
        min-height: 522rpx;
        background: white;
        border-radius: 20rpx;
        -webkit-border-radius: 20rpx;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        -webkit-transform: translate(-50%, -50%);
    }

    .fankuicard {
        width: 75%;
        height: 800rpx;
        background: white;
        border-radius: 20rpx;
        -webkit-border-radius: 20rpx;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        -webkit-transform: translate(-50%, -50%);
        padding: 0 30rpx;
    }

    .finishcard {
        width: 75%;
        height: 680rpx;
        background: white;
        border-radius: 20rpx;
        -webkit-border-radius: 20rpx;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        -webkit-transform: translate(-50%, -50%);
        padding: 0 30rpx;
    }

    .cardtitle {
        width: 100%;
        margin: 0 auto;
        text-align: center;
        display: block;
        margin-top: 29rpx;
        margin-bottom: 37rpx;
    }

    .cardline {
        display: block;
        margin: 0 auto;
        width: 75%;
        border-bottom: 1rpx solid rgba(128, 128, 128, 0.5);
        overflow: hidden;
        height: 75rpx;
        line-height: 75rpx;
        font-size: 24rpx;
        margin-bottom: 18rpx;
    }

    .linetitel {
        float: left;
    }

    .linedetail {
        text-align: right;
        float: right;
        width: 310rpx;
        /*background:red;*/
        overflow: hidden;
    }

    .sure {
        margin-top: 40rpx;
        width: 84%;
        margin: 0 auto;
    }

    .check {
        width: 100%;
        height: 88rpx;
        display: block;
        background: white;
        color: #2d8cf0;
    }

    .ckitem {
        height: 100%;
        width: 50%;
        display: block;
        padding: 0 20rpx;
        box-sizing: border-box;
        line-height: 88rpx;
    }

    .ckitem1 {
        float: left;
        text-align: left;
    }

    .ckitem2 {
        float: right;
        text-align: right;
    }

    .picker {
        width: 450rpx;
        height: 50rpx;
        line-height: 50rpx;
        text-align: center;
        font-size: 28rpx;
        border: 1rpx solid #0064fa;
        color: #0064fa;
        border-radius: 10rpx;
        display: inline-block;
        float: left;

    }

    .picker2 {
        width: 325rpx;
        height: 50rpx;
        line-height: 50rpx;
        text-align: center;
        font-size: 28rpx;
        border: 1rpx solid #a7a7a7;
        color: #a7a7a7;
        margin-left: 30rpx;
        border-radius: 10rpx;
        display: inline-block;
        float: right;
    }

    .reasons {
        display: block;
        width: 100%;
        height: 50rpx;
    }

    .fklytxt {
        margin: 25rpx 0;
        display: block;
        font-size: 27rpx;
    }

    .fklyDesc {
        width: 100%;
        height: 178rpx;
        background: #f6f6f6;
        border: 1rpx solid #e1e1e1;
        border-radius: 10rpx;
        -webkit-border-radius: 10rpx;
        font-size: 25rpx;
        line-height: 35rpx;
        text-align: justify;
        overflow: hidden;
        display: block;
        padding: 10rpx;
        box-sizing: border-box;

    }

    .fkbtns {
        width: 258rpx;
        height: 60rpx;
        float: left;
        line-height: 62rpx;
        display: block;
        font-size: 25rpx;
        position: absolute;
        left: 28rpx;
        top: 560rpx;
    }

    .voiceplay {
        background: #a9e97b;
    }

    .voice {
        width: 50rpx;
        height: 50rpx;
        margin-top: 5rpx;
        margin-left: 2rpx;
        float: left;
    }

    .deleteVoice {
        position: absolute;
        width: 82rpx;
        height: 47rpx;
        left: 294rpx;
        top: 569rpx;
    }

    .cardclose {
        width: 45rpx;
        height: 45rpx;
        right: 20rpx;
        top: 18rpx;
        position: absolute;
    }

    .fkbtn {
        width: 100%;
        display: block;
        margin-top: 138rpx;
    }

    .finishtips {
        width: 100%;
        text-align: left;
        font-size: 27rpx;
        margin-top: -16rpx;
        display: block;
        padding: -1 0 0 11rpx;
        padding-left: 18rpx;
        margin-bottom: 27rpx;
        margin-top: -16rpx;

    }

    .uoloadimgs {
        display: inline-block;
        width: 136rpx;
        height: 136rpx;
        text-align: center;
        line-height: 107rpx;
        font-size: 78rpx;
        height: 150rpx;
        border: 2rpx solid #cbcbcb;
        background: #f3f3f3;
        margin: 10rpx 18rpx;
        /*border-radius : 16rpx;*/
        color: #d0d0d0;
        position: relative;
        margin-top: 63rpx;
        margin-bottom: 56rpx;
    }

    .tips {
        position: absolute;
        width: 100%;
        text-align: center;
        left: 0;
        top: 57rpx;
        font-size: 20rpx;
        color: #d0d0d0;
    }

    .finishcard .imgbox {
        width: 100%;
        height: 100%;
        position: absolute;
        left: 0;
        top: 0;
        overflow: hidden;
    }

    .uploadedimg {
        position: absolute;
        left: 0;
        top: 50%;
        width: 100%;
        /*height   : 100%;*/
        z-index: 1;
        transform: translateY(-50%);
        -webkit-transform: translateY(-50%);
        background: white;
    }

    .delete {
        position: absolute;
        right: -18rpx;
        top: -18rpx;
        width: 36rpx;
        height: 36rpx;
        z-index: 2;
    }

    .weixiug {
        width: 656rpx;
        /*min-height: 300rpx;*/
        display: block;
        margin: 0 auto;
        background: rgba(255, 209, 119, 0.13);
        border: 1rpx solid rgba(76, 115, 137, 0.29);
        margin-bottom: 10rpx;
        border-radius: 12rpx;
        overflow: hidden;
    }

    .lists {
        width: 100%;
        height: 60rpx;
        line-height: 60rpx;
        /*background: #deeaff;*/
        box-sizing: border-box;
        padding: 0 10rpx;
        position: relative;
    }

    .listsleft {
        display: block;
        width: 30%;
        height: 100%;
        float: left;
    }

    .listsright {
        float: left;
        display: block;
        width: 70%;
        height: 100%;
    }

    .finishend {
        display: block;
        width: 80%;
        margin-top: 30rpx;
    }

    .infos {
        display: block;
        width: 80%;
        margin-top: 30rpx;
        margin-bottom: 30rpx;
    }

    .finishend {
        display: block;
        width: 80%;
        margin-top: 30rpx;
        text-align: center;
        margin: 0 auto;
        margin-top: 30rpx;
    }

    .uoloadimgs {
        margin-top: 25rpx;
        margin-bottom: 33rpx;
    }
</style>
