<style scoped>
.block-clock {
    width:540px;
    height: 80px;
    margin:15px auto;
    border:1px solid #dddddd;
}
.countdown-img {
    float: left;
    height:80px;
    line-height: 80px;
}
.countdown-img img {
    width:60px;
    height: 60px;
}
.countdown-text {
    padding-right: 60px;
    text-align: center;
}
.countdown-text p {
    margin-bottom:10px;
    font-family: 'AvenirNext-Medium';
    color: #5E6369;
    font-size: 16px;
}
.label {
    font-size: 18px;
    margin-left: 15px;
}
.detail-link {
    text-align: center;
    cursor: pointer;
}
.detail-link i {
    font-size:20px;
}
.proportion-title {
    /* width:230px; */
}
.proportion-title p {
    margin:0
}
.proportion-info-icon {
    cursor: pointer;
}

.btn-history {
    float: right;
}
</style>
<template>
    <div>
        <breadcrumb :current="$t('nodeMgmt.stakeAuthorization')" v-on:backEvent="handleRouteBack"></breadcrumb>
            <a-button type="primary" class="btn-next btn-history" @click="toStakeHistory">{{$t('nodeMgmt.stakeHistory')}}</a-button>
        <div class="block-clock">
            <div>
                <div class="countdown-img">
                    <img src="../../../assets/countdown.svg" alt="">
                </div>
                <div class="countdown-text">
                    <p>{{$t('nodeMgmt.toNextRound')}}</p>
                    <span class="font-medium-black" style="font-size:20px;">{{countdown}}</span>
                    <span class="font-medium label">{{$t('nodeMgmt.blocks')}}</span>
                </div>
            </div>
        </div>
        <a-table :columns="columns"
        :dataSource="node_list"
        >
        <div slot="nodeProportionTitle" class="proportion-title">
            <p>{{$t('nodeMgmt.proportionNextRound')}}
                <a-icon type="info-circle-o" class="proportion-info-icon" @click="showProportionTip"/>
            </p>
            </div>
        <a slot="name" slot-scope="text, record" @click="handleNodeDetail(record)">{{text}}</a>
        <div slot="action" slot-scope="text, record" class="detail-link">
            <a-icon type="arrow-right" @click="handleAuthorizeLogin(record)"/>
        </div>

        </a-table>
    </div>
</template>
<script>
import Breadcrumb from '../../Breadcrumb'
import {mapState} from 'vuex'
const {BrowserWindow} = require('electron').remote;

export default {
    name: 'NodeList',
    components: {
        Breadcrumb
    },
    data() {
        const columns = [
            {
                title: this.$t('nodeMgmt.rank'),
                dataIndex: 'rank',
                key:'rank'
            },
            {
                title: this.$t('nodeMgmt.name'),
                dataIndex: 'name',
                key: 'name',
                scopedSlots: {customRender: 'name'}
            },
            {
                // title: this.$t('nodeMgmt.nodeProportion'),
                dataIndex: 'nodeProportion',
                key: 'nodeProportion',
                slots: {title: 'nodeProportionTitle'}
            },
            {
                title: this.$t('nodeMgmt.currentStake'),
                dataIndex: 'currentStake',
                key: 'currentStake'
            },
            {
                title: this.$t('nodeMgmt.process'),
                dataIndex: 'process',
                key: 'process'
            },
            {
                title: '',
                key: 'action',
                scopedSlots: {customRender:'action'}
            }
        ]
        const data = [
            
        ]
        return {
            columns,
            intervalId:''
        }
    },
    mounted(){
        //loop to fetch data
        // this.$store.dispatch('showLoadingModals');
        this.$store.dispatch('fetchNodeList')
        this.$store.dispatch('fetchBlockCountdown')
        this.intervalId = setInterval(()=>{
            // this.$store.dispatch('fetchNodeList')
            this.$store.dispatch('fetchBlockCountdown')
        }, 5000)  
    },
    beforeDestroy(){
        clearInterval(this.intervalId);
    },
    computed:{
        ...mapState({
            node_list: state => state.NodeAuthorization.node_list,
            countdown: state => state.NodeAuthorization.countdown
        })
    },
    methods: {
        handleRouteBack() {
            this.$router.go(-1);
        },
        handleAuthorizeLogin(record, item){
            console.log(record)            
            this.$store.commit('UPDATE_CURRENT_NODE', {current_node: record})
            this.$router.push({name: 'AuthorizeLogin'})
        },
        handleNodeDetail(record, item) {
            console.log(record);
           let win = new BrowserWindow({width: 800, height: 600, center: true});
            win.on('closed', () => {
            win = null
            })

            // Load a remote URL
            win.loadURL(record.detailUrl);
        },
        showProportionTip() {
            const h = this.$createElement
            const title = this.$t('nodeMgmt.proportionNextRound')
            const content = this.$t('nodeMgmt.proportionNextRoundTip')
            this.$info({
                title: title,
                content: h('div',{}, [
                h('p', content),
                ]),
                onOk() {},
            });
        },
        toStakeHistory() {
            this.$router.push({name: 'StakeHistory'})
        }
    }
}
</script>
