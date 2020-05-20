<template>
    <div>
        <van-address-list
                v-model="chosenAddressId"
                :list="list"
                default-tag-text="默认"
                @add="onAdd"
                @edit="onEdit"
                @select="onselect"
        />
    </div>
</template>

<script>
    import { Toast } from 'vant';
    import axios from "axios";
    export default {
        data() {
            return {
                chosenAddressId: 0,
                list: [
                    {
                        areaCode: "440303" ,
                        id: 21,
                        name: "张三" ,
                        tel: "13678787878" ,
                        address: "广 东省深圳市罗湖区科技路123号456室"
                    },
                    {
                        areaCode: "330104" ,
                        id: 24,
                        name :"小明",
                        tel: "13636363636",
                        address: "浙江省杭州市江干区789号"
                    }
                ]
            }
        },
        created(){
            const _this = this
            axios.get('http://localhost:8000/address/list').then(function (response) {
                _this.list=response.data.data
            })
        },
        methods: {
            onAdd() {
                this.$router.push('/addressNew')
            },
            onEdit(item) {
                let data = JSON.stringify(item)
                this.$router.push({path:'/addressEdit',query:{item:data}})
            },
            onselect(item){
                let orderForm = {
                    name: item.name,
                    tel: item.tel,
                    address: item.address,
                    specsId: this.$store.state.specsId,
                    quantity: this.$store.state.quantity
                }
                const _this = this
                axios.post('http://localhost:8000/order/create',orderForm).then(function (resp) {
                    console.log(orderForm)
                     if(resp.data.code == 0){
                        let instance = Toast('下单成功');
                        setTimeout(() => {
                            instance.close();
                            _this.$router.push('/detail?orderId='+resp.data.data.orderId)
                        }, 1000)
                     }
                })
            }
        }
    }
</script>