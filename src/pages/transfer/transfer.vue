<template>
    <div>
        <div class="container">
            <div class="title">
                <span>{{$t('transfer.transfer')}}</span>
            </div>
        </div>
        <div class="container">
            <div class="transfer-panel">
                <div class="form-horizontal">
                    <div class="col-sm-12">
                        <label for="firstname" class="col-sm-2 control-label" style="padding-top:20px;">{{$t('transfer.title1')}}:</label>
                        <div class="col-sm-3">
                            <div class="dropdown">
                                <div type="button" class="btn dropdown-toggle select-nel" id="assets" data-toggle="dropdown" :class="balances.length>0 ? '' : 'select-disabled' ">
                                    <div class="select-title">{{balance.names}}</div>
                                    <div class="select-caret">
                                        <span class="caret"></span>
                                    </div>
                                </div>
                                <ul class="dropdown-menu dropdown-nel" role="menu" aria-labelledby="assets">
                                    <li role="presentation" v-for="balance in balances" :class="asset==balance.asset?'active':''" :key="balance.asset" :value="balance.asset">
                                        <a role="menuitem" tabindex="-1" @click="choose(balance.asset)">{{balance.names}}</a>
                                    </li>
                                </ul>
                            </div>
                        </div>
                        <div class="col-sm-4" style="padding-top:20px;">
                            <span> &nbsp;&nbsp;&nbsp;&nbsp; {{balance.balance}} {{balance.names ? balance.names +" "+ $t('transfer.msg5') : ""}} </span>
                        </div>
                    </div>
                    <div class="col-sm-12" :class="addrerr!=0?(addrerr == 1 ?'success':'err') :''">
                        <label for="" class="col-sm-2 control-label">  
                            <div style="padding-top:40px;">{{$t('transfer.title2')}}:</div>
                        </label>
                        <div class="col-sm-7">
                            <div style="padding-top:30px;">
                                <input type="text" v-model="target" class="nel-input big" :placeholder="$t('transfer.placeholder') " @input="verify_addr" autocomplete="off">
                            </div>
                            <p v-if="isDomain">
                                <img class="transfer-icon" src="../../../static/img/transfer.png" alt="">
                                <span class="map-address">{{toaddress}}</span>
                            </p>
                        </div>
                        <div class="col-sm-3 mess">
                            <p v-if="addrerr==2||addrerr==4"><img src="../../../static/img/wrong.svg" alt="">&nbsp;&nbsp;{{$t('transfer.errdomain')}} </p>
                            <p v-if="addrerr==3"><img src="../../../static/img/wrong.svg" alt="">&nbsp;&nbsp;{{$t('transfer.msg1')}} </p>
                            <p v-if="addrerr==1"><img src="../../../static/img/correct.svg" alt="">&nbsp;{{tipAddress}}</p>
                        </div>
                    </div>
                    <div class="col-sm-12" :class="amounterr!=''?(amounterr == 'true' ?'err':'success') :''">
                        <label for="" class="col-sm-2 control-label">
                            <div style="padding-top:40px;">{{$t('transfer.title3')}}:</div>
                        </label>
                        <div class="col-sm-7">
                            <div style="padding-top:30px;">
                                <input type="number" v-model="amount" class="nel-input big" @change="verify_Amount" @input="verify_Amount" autocomplete="off">
                            </div>
                        </div>
                        <div class="col-sm-3 mess">
                        </div>
                    </div>
                    <div class="col-sm-12" style="padding-top:30px;">
                        <div class="col-sm-6"></div>
                        <div class="col-sm-3">
                            <button class="btn btn-link">{{$t('transfer.details')}}</button>
                            <button class="btn btn-nel btn-big" :class="{'btn-disabled':!isAddress||!isNumber}" @click="send" :disabled="!isAddress||!isNumber">{{$t('transfer.send')}}</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="container">
            <div class="title">
                <span>{{$t('transfer.title4')}}</span>
            </div>
        </div>
        <div class="container">
            <div class="history-panel">
                <div>
                    <div class="title"></div>
                    <div v-for="tx in txs" class="history" :key="tx.index">
                        <div class="number" :class="tx.txtype">
                            {{tx.txtype == 'out'?'+ ':'- '}}{{tx.value.toString()}}&nbsp;{{tx.assetname}}</div>
                        <div class="address">{{tx.txtype == 'out'?$t('transfer.from'):$t('transfer.to')}} : {{tx.address}}</div>
                        <div class="time">
                            <a :href="'https://scan.nel.group/#mainnet/transaction/'+tx.txid" target="_blank">
                                {{tx.txid.substring(0, 4) + '...' + tx.txid.substring(tx.txid.length - 4)}}
                            </a> &nbsp;{{tx.time}}
                            <div v-if="tx.waiting">({{$t('transfer.waiting')}})</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="page" v-if="cutshow">
                <div class="page-previous" :class="txpage<=1? 'disabled':''" @click="cutPage('pre')">
                    <img src="../../../static/img/lefttrangle.svg" alt="">
                </div>
                <div style="width:1px;"></div>
                <div class="page-next" :class="nextpage?'':'disabled'" @click="cutPage('next')">
                    <img src="../../../static/img/righttrangle.svg" alt="">
                </div>
            </div>
        </div>        
        <v-toast ref="toast" ></v-toast>
    </div>
</template>

<script lang="ts" src="./transfer.ts">
</script>

<style lang="less">
.select-nel {
  background: #198cee;
  border-radius: 5px;
  height: 56px;
  margin: 0 auto;
  padding: 0;
}
.select-disabled {
  background: #77bcf6;
  cursor: not-allowed;
}
.select-title {
  padding-top: 15px;
  float: left;
  width: 110px;
  font-family: PingFangSC-Medium;
  font-size: 18px;
  color: #ffffff;
  line-height: 16px;
}
.select-caret {
  padding-top: 15px;
  margin-top: 0;
  width: 36px;
  float: right;
  background: #ffffff;
  border-radius: 0 5px 5px 0;
  height: 54px;
}
.mess {
  padding-top: 45px;
}
.line-title {
  padding-top: 40px;
  padding-bottom: 20px;
}
.balance-type {
  font-family: PingFangSC-Medium;
  font-size: 20px;
  line-height: 20px;
}
.balance-amount {
  font-family: PingFangSC-Medium;
  font-size: 30px;
  line-height: 30px;
}
.line-title span {
  font-size: 16px;
  line-height: 16px;
}
.transfer-panel {
  background: #ffffff;
  border-radius: 5px;
  height: 414px;
}
.transfer-panel .form-horizontal {
  padding-top: 2.9%;
}
.transfer-panel span,
.transfer-panel label {
  font-family: PingFangSC-Medium;
  font-size: 16px;
  line-height: 16px;
  font-weight: unset;
}
.transfer-panel span {
  font-family: PingFangSC-Regular;
  font-size: 16px;
  color: #198cee;
  line-height: 16px;
}
.history-panel {
  background: #fff;
  color: #333;
  border-radius: 5px;
  padding-bottom: 30px;
  margin-bottom: 15px;
}
.history {
  border: 1px solid #b2b2b2;
  border-radius: 5px;
  margin-bottom: 30px;
  margin-left: 30px;
  width: 872px;
}
.history .number {
  font-family: PingFangSC-Medium;
  font-size: 20px;
  line-height: 20px;
}
.number.out {
  color: #2dde4f;
}
.number.in {
  color: #ff6a6a;
}
.history .address {
  font-family: PingFangSC-Medium;
  font-size: 16px;
  line-height: 16px;
}
.history > div {
  margin-left: 15px;
  margin-right: 15px;
  padding-bottom: 8px;
  padding-top: 8px;
}
.history .time {
  border-top: 1px solid #f2f2f2;
}
</style>

