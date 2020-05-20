<template>
  <div class="home">
    <van-tabs @click="onClick">
      <van-tab v-for="index in categories.length" :title="categories[index-1].name" >
        <template #title> <van-icon name="more-o" />{{categories[index-1].name}} </template>
        <van-card v-for="(item,index) in phones"
                :price="item.price"
                :desc="item.desc"
                :title="item.title"
                :thumb="item.thumb"
        >
          <template #tags>
            <van-tag plain type="danger" v-for="tag in item.tag" color="#f2826a">{{tag.name}}</van-tag>
          </template>
          <template #footer>
<!--            <van-button size="mini">加购</van-button>-->
            <van-button size="mini" @click="onbuy(index)">购买</van-button>
          </template>
        </van-card>
      </van-tab>
    </van-tabs>

    <van-sku
            v-model="show"
            :sku="sku"
            :goods="goods"
            :hide-stock="sku.hide_stock"
            @buy-clicked="onBuyClicked"
    >

      <!-- 自定义 sku actions -->
      <template #sku-actions="props">
        <div class="van-sku-actions">
          <!-- 直接触发 sku 内部事件，通过内部事件执行 onBuyClicked 回调 -->
          <van-button
                  square
                  size="large"
                  type="danger"
                  @click="props.skuEventBus.$emit('sku:buy')"
          >
            买买买
          </van-button>
        </div>
      </template>
    </van-sku>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import axios from 'axios'
export default {
  name: 'Home',
  components: {
    HelloWorld
  },
  data(){
    return{
      categories:[
        {
          name: '极光兰',
          type:1
        },
        {
          name: 'yellow',
          type:2
        },
        {
          name: 'blue',
          type:3
        },
        {
          name: 'black',
          type:4
        },
      ],
      phones:[
        {
          id:5,
          title:'华为',
          price:3863,
          desc:'blue',
          tag:[
            {
              name:'very good'
            },
            {
              name:'cheap'
            }
          ],
          thumb:"../static/phone.jpg"
        },
        {
          id:9,
          title:'xmi',
          price:3163,
          desc:'yellow',
          tag:[
            {
              name:'very nice'
            },
            {
              name:'so cheap'
            }
          ],
          thumb:"../static/phone.jpg"
        },
      ],
      show:false,
      sku:{
        tree:[
          {
            k:"规格",
            v:[
              {
                id:1,
                name:"32GB",
                imgUrl:"../static/phone.jpg",
                previewImgUrl:"../static/phone.jpg"
              },
              {
                id:2,
                name:"64GB",
                imgUrl:"../static/phone.jpg",
                previewImgUrl:"../static/phone.jpg"
              }
            ],
            k_s:"s1"
          }
        ],
        list:[
          {
            s1:1,
            price:287500,
            stock_num:115
          },
          {
            s1:2,
            price:230400,
            stock_num:5
          }
        ],
        price:"2875",
        stock_num:120,
        none_sku:false,
        hide_stock:false
      },
      goods:''
    }
  },
    created() {
      const _this=this;
      axios.get('http://localhost:8000/phone/index').then(function (response) {
          _this.phones=response.data.data.phones
          _this.categories=response.data.data.categories
      })
    },
    methods:{
    onClick(index){
      //alert(index)
        const _this=this
        axios.get('http://localhost:8000/phone/findByCategoryType/'+this.categories[index].type).then(function (response) {
            _this.phones=response.data.data
        })
    },
    onbuy(index){
        this.show=true
        const _this=this
        axios.get('http://localhost:8000/phone/findSpecsByPhoneId/'+this.phones[index].id).then(function (response) {
            _this.goods=response.data.data.goods
            _this.sku=response.data.data.sku
        })
        this.goods={

        }
        this.sku={
            tree:[
                {
                    k:"规格",
                    v:[
                        {
                            id:1,
                            name:"32GB",
                            imgUrl:"../static/phone.jpg",
                            previewImgUrl:"../static/phone.jpg"
                        },
                        {
                            id:2,
                            name:"64GB",
                            imgUrl:"../static/phone.jpg",
                            previewImgUrl:"../static/phone.jpg"
                        }
                    ],
                    k_s:"s1"
                }
            ],
            list:[
                {
                    s1:1,
                    price:287500,
                    stock_num:115
                },
                {
                    s1:2,
                    price:230400,
                    stock_num:5
                }
            ],
            price:"2875",
            stock_num:120,
            none_sku:false,
            hide_stock:false
        }
    },
    onBuyClicked(item){
        this.$store.state.specsId=item.selectedSkuComb.s1
        this.$store.state.quantity=item.selectedNum
        this.$router.push('/addressList')
    }
  }
}
</script>
