<!--引用scroller3-1组件的demo-->
<template>
    <div class="home">

        <v-scroll :on-refresh="onRefresh" :on-infinite="onInfinite"  :loading-text="loadingText" >
            <ul class="list">
                <li v-for="item in articleList">{{item.title}}</li>
            </ul>
        </v-scroll>
    </div>
</template>

<script>
    import { AjaxPlugin, XButton,Group, Cell, XInput} from 'vux'
    import Scroll from '../../common/pull-refresh';

    export default {
        name: 'home',
        components: {
            'v-scroll': Scroll,
            Group,
            XButton,
            Cell,
            XInput,
            AjaxPlugin
        },
        data () {
            return {
                articleList:this.getArticleList(1),
                loadingText:'加载中...',
                counter : 1, //当前页面
                num : 20,  // 一次显示多少条
                listdata: [], // 下拉更新数据存放数组
                downdata: []  // 上拉更多的数据存放数组
            }
        },
        mounted : function(){
            //this.getArticleList();
        },
        methods: {




            getArticleList: function (catId) {
                let _this = this
                getDate('/index.php',{params:{m:'jsonp',c:'index',a:'lists',catid:59}},function(data){
                    let arr = data.result.data;
                    _this.articleList=arr;
                    if(arr.length>=_this.num){
                        _this.counter++;
                    }
                })
                return []
            },
            onRefresh(done) {
                let vm = this;
                vm.loadingText='加载中……';
                getDate('/index.php',{params:{m:'jsonp',c:'index',a:'lists',catid:59}},function(data){
                    let arr = data.result.data;
                    vm.articleList=arr;
                    if(arr.length>=vm.num){
                        vm.counter=2;
                    }
                    done() // call done
                })
            },
            onInfinite(done) {
                let vm = this;
                getDate('/index.php',{params:{m:'jsonp',c:'index',a:'lists',catid:59,page:vm.counter}},function(data){
                    let arr = data.result.data;
                    vm.articleList=vm.articleList.concat(arr);
                    if(arr.length<vm.num){
                        vm.loadingText='加载完毕……';
                        //vm.$el.querySelector('.load-more').style.display = 'none';
                        return;
                    }else{
                        vm.counter++;
                    }
                    done() // call done
                })
            }
        }
    }


    function getDate(url,params,fn) {
        AjaxPlugin.$http.get(url, params)
            .then((response) => {
                if (fn) fn(response.data)
            })


    }

    function htttpRequest (url, params, cb) {
        let dataStr = ''
        for (let key in params) {
            dataStr += key + '=' + params[key]
        }

        dataStr = dataStr.substr(0, dataStr.length - 1)
        console.log(dataStr);
        AjaxPlugin.$http.post(url, params)
            .then((response) => {
                if (cb) cb(response.data)
            })
    }



</script>



<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    h1, h2 {
        font-weight: normal;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        flex: 1;
        margin: 10px ;
        border-bottom:#ddd solid 1px;
    }

    a {
        color: #42b983;
    }
</style>