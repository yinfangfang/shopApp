<template>
    <div id="app" v-cloak>
        <div class="tab">
            <a @click="flag = true">上架</a> <a @click="flag = false">下架</a>
        </div>
        <hr>
        <div v-if="flag">
            <div class="put">

                    <table class="tab1">
                        <tr>
                            <td><span>请添加商品图片</span></td>
                            <td><input type="file" ref="pic" multiple="multiple" accept="image/png,image/gif,image/jpeg" @change="getImg"></td>
                        </tr>
                        <tr>
                            <td><span>请输入品牌名称</span></td>
                            <td>
                                <select value="" v-model="brand_name" v-if="brandInfo" >
                                    <option :key=index v-for="(item,index) in brandInfo.data">{{item.name}}</option>
                                </select>
                            </td>
                        </tr>
                        <tr>
                            <td><span>请输入商品名称</span></td>
                            <td><input type="text" value="" v-model="name"></td>
                        </tr>
                        <tr>
                            <td><span>请输入商品详情</span></td>
                            <td><input type="text" value="" v-model="good_detail"></td>
                        </tr>
                        <tr>
                            <td><span>请输入可选颜色</span></td>
                            <td><input type="text" value="" v-model="color"></td>
                        </tr>
                        <tr>
                            <td><span>请输入可选规格</span></td>
                            <td><input type="text" value="" v-model="size"></td>
                        </tr>
                        <tr>
                            <td><span>请输入商品类型</span></td>
                            <td><input type="text" value="" v-model="type"></td>
                        </tr>
                        <tr>
                            <td><span>请输入商品价格</span></td>
                            <td><input type="text" value="" v-model="price"></td>
                        </tr>
                        <tr>
                            <td><span>请输入商品运费</span></td>
                            <td><input type="text" value="" v-model="fre_price"></td>
                        </tr>
                        <tr>
                            <td><span>请输入商品库存</span></td>
                            <td><input type="text" value="" v-model="repertory"></td>
                        </tr>
                        <tr>
                            <td><span>请输入交易备注</span></td>
                            <td><input type="text" value="" v-model="tip"></td>
                        </tr>
                        <tr>
                            <td><span>请输入一级分类</span></td>
                            <td>
                                <select  v-model="ProductActive" v-if="typeOneInfo" @change="changeProduct($event)">
                                    <option :value="item.id" :key="index" v-for="(item,index) in typeOneInfo.data">{{item.name}}</option>
                                </select>
                            </td>
                        </tr>
                        <tr>
                            <td><span>请输入二级分类</span></td>
                            <td>
                                <select  v-if="typeTwoInfo" v-model="Prod">
                                    <option :key=index :value="item.id" @change="chang(item.id)" v-for="(item,index) in typeTwoInfo.data" >{{item.name}}</option>
                                </select>
                            </td>
                        </tr>
                        <tr>
                            <td><span>请输入具体款式</span></td>
                            <td><input type="text" value="" v-model="style"></td>
                        </tr>
                        <tr>
                            <td colspan="2"><input type="submit" value="提交" @click="confirmchange"></td>
                        </tr>
                    </table>

            </div>
        </div>
        <div v-else >
            <ul>
                <li :key="index" v-for="(item,index) in downInfo.data ">
                    <div class="Allgoods">
                        <img :src="item.img_url.split('\'')[1]">
                        <h3>{{item.brand_name}}</h3>
                        <p class="des">{{item.name}}</p>
<!--                        <span class="goodsid">{{item.goods_id}}</span>-->
                        <span class="price">￥{{item.fre_price}}</span>
                        <span class="btn"  @click="down(item.goods_id)" >下架</span>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    import {Toast} from "vant"
    export default {
        name: "product",
        data(){
            return {
                img:'',
                brand_name: '',
                name: '',
                price: '',
                good_detail:'',
                color:"",
                size: '',
                fre_price: '',
                repertory: '',
                tip:'',
                parent:"",
                child:'',
                style:"",
                type:"",
                flag:true,
                brandInfo:null,
                typeTwoInfo:null,
                typeOneInfo:null,
                ProductActive:1,
                Prod:2,
                downInfo: null,
                num:null
            }
        },
        methods: {
            down(id){
                // console.log(this.num)
                fetch('http://119.3.190.106:5000/shop/commodity/down_goods/?id='+id+'&token=ZmU2NzZmMWEwNWZlNGVkNjlmYTdiNjUwZTQ5MmNhNTU=').then(res=>{
                    res.json().then(data => {
                        this.downInfo = data;
                        console.log(data)
                    })
                })
                this._downInfo();
            },
            getImg(event) {
                let that = this
                this.file = event.target.files[0];
                var reader = new FileReader();
                var AllowImgFileSize = 2100000; //上传图片最大值(单位字节)（ 2 M = 2097152 B ）超过2M上传失败
                var file = this.$refs.pic.files[0];
                // eslint-disable-next-line no-unused-vars
                var imgUrlBase64;
                if (file) {
                    //将文件以Data URL形式读入页面
                    imgUrlBase64 = reader.readAsDataURL(file);
                    // eslint-disable-next-line no-unused-vars
                    reader.onload = function (e) {
                        //var ImgFileSize = reader.result.substring(reader.result.indexOf(",") + 1).length;//截取base64码部分（可选可不选，需要与后台沟通）
                        if (AllowImgFileSize != 0 && AllowImgFileSize < reader.result.length) {
                            alert( '上传失败，请上传不大于2M的图片！');
                            return;
                        }else{
                            //执行上传操作
                            // console.log(reader.result);
                            that.img =  reader.result;
                        }
                    }
                }
            },
            changeProduct(event) {
                this.ProductActive = event.target.value; //获取商品ID，即option对应的ID值
                this. _initTypeTwoInfo();
            },
            chang(event) {
                this.Prod = event.target.value; //获取商品ID，即option对应的ID值
            },
            confirmchange() {
                let token = localStorage.getItem("token")
                console.log(this.name)
                console.log(this.price)
                console.log(this.good_detail)
                console.log(this.color)
                console.log(this.type)
                console.log(this.Prod)
                console.log(this.ProductActive)
                fetch('http://10.35.167.125:5000/shop/commodity/up_goods/', {
                    method: "POST",
                    mode: "cors",
                    headers: {
                        'Accept': 'application/json',
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        token: token,
                        name:this.name,
                        img_url:this.img,
                        price:this.price,
                        brand_name:this.brand_name,
                        good_detail:this.good_detail,
                        color:this.color,
                        size:this.size,
                        type:this.type,
                        fre_price:this.fre_price,
                        repertory:this.repertory,
                        tip:this.tip,
                        parent:this.ProductActive,
                        child:this.Prod,
                        style:this.style
                    })
                }).then(res => {
                    return res.json();
                }).then(data => {
                    console.log(data);
                    Toast(data.msg)
                }).catch(err => {
                    console.log(err);
                })
            },
            _initBrandInfo(){
                fetch('http://119.3.190.106:5000/shop/commodity/brand_name/').then(res=>{
                    res.json().then(data => {
                        this.brandInfo = data;
                    })
                })
            },
            _initTypeOneInfo(){
                fetch('http://119.3.190.106:5000/shop/commodity/classify_first/').then(res=>{
                    res.json().then(data => {
                        this.typeOneInfo = data;
                    })
                })
            },
            _initTypeTwoInfo(){
                fetch('http://119.3.190.106:5000/shop/commodity/classify_second/?id='+this.ProductActive).then(res=>{
                    res.json().then(data => {
                        this.typeTwoInfo = data;
                    })
                })
            },
            _downInfo(){
                fetch('http://119.3.190.106:5000/shop/commodity/online_goods/?token=ZmU2NzZmMWEwNWZlNGVkNjlmYTdiNjUwZTQ5MmNhNTU=').then(res=>{
                    res.json().then(data => {
                        this.downInfo = data;
                    })
                })
            }
        },
        beforeMount(){
            this._initBrandInfo();
            this._initTypeOneInfo();
            this._initTypeTwoInfo();
            this._downInfo();
        }
    }
</script>

<style scoped>
    *{
        margin: 0;
        padding: 0;
    }
    body{
        background-color:white;
    }
    #app{
        padding-bottom: 0.4rem;
    }
    html{
        font-size: 26.67vw;
        overflow-x: hidden;
    }
    select{
        width: 1.5rem;
        border: none;
        background:none;
    }
    select option{
        text-align: center;
    }
    tr{
        font-size: 0.1rem;
    }
    th{
        font-size: 0.1rem;
    }
    h3{
        font-size: 0.2rem;
        text-align: center;
    }
    .put{
        font-size: 0.14rem;
    }
    table {
        width: 3.5rem;
        border-collapse: collapse;
        margin: 0 auto;
        text-align: center;
    }
    table td, table th {
        border: 1px solid #cad9ea;
        color: #666;
        height: 0.4rem;
    }
    table thead th {
        background-color: #CCE8EB;
        width: 100px;
    }
    table tr:nth-child(odd) {
        background: #fff;
    }
    table tr:nth-child(even) {
        background: #F5FAFA;
    }
    input{
        background:none;
        outline:none;
        border:0px;
        height: 0.4rem;
    }
    input[type="text"]:focus, input[type="password"]:focus {
        outline: none;
    }
    input[type="file"]{
        font-size: 0.1rem;
        line-height: 0.4rem;
        vertical-align: middle;
        width: 1rem;
    }
    input[type="submit"]{
        font-size: 0.1rem;
        line-height: 0.4rem;
    }
    .tab{
        display: flex;
    }
    .tab a{
        height: 0.3rem;
        display: inline-block;
        flex: 1;
        text-align: center;
        line-height: 0.3rem;
    }
    ul{
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
    }
    li{
        width: 1.75rem;
        margin-top: 0.08rem;
        padding-left:0.08rem;
    }
    ul li:nth-child(2n){
        padding-right: 0.08rem;
    }
    .Allgoods{
        width: 1.75rem;
        height: 2.77rem;
        border: 1px solid rgba(0,0,0,0.1);
        position: relative;
        background-color: white;
    }
    .price{
        position: absolute;
        bottom: 0.1rem;
        left: 0.1rem;
        color: #2c3e50;
        font-weight: bold;
    }
    .Allgoods img{
        width: 100%;
        height:1.765rem ;
    }
    .des{
        overflow: hidden;
        text-overflow:ellipsis;
        word-wrap: break-spaces;
    }
    .btn{
        display: inline-block;
        width: 0.6rem;
        height: 0.25rem;
        background-color: lightsalmon;
        position: absolute;
        right: 0.1rem;
        bottom: 0.1rem;
        line-height: 0.25rem;
        text-align: center;
        border-radius:0.1rem ;
    }
    .goodsid{
        font-size: 0.1rem;
        color: rgba(0,0,0,0);
    }
</style>