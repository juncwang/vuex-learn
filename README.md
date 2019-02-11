# vuex-learn

### vuex 安装

* `npm install vuex --save`

### vuex 使用

* 创建一个 js 文件, 例如 `store/store.js`
* 在该文件内分别引入 `vue,vuex`, 并用 `vue` 使用 `vuex`
    ```js
    import Vue from 'vue'
    import Vuex from 'vuex'

    Vue.use(Vuex)
    ```
* 在 `store.js` 文件内装载数据
    ```js
    const store = new Vuex.Store({
        // 是否采用严格模式
        strict: false,
        // 存储数据
        state: {
            products: [
                {name: "马云", price: 200},
                {name: "马化腾", price: 140},
                {name: "马冬梅", price: 20},
                {name: "马蓉", price: 10},
            ]
        },
        // 获取数据
        getters:{
            saleProducts: (state) => {
                var saleProducts = state.products.map(
                    product => {
                        return {
                            name: "**" + product.name + "**",
                            price: product.price/2
                        }
                    })
                return saleProducts
            }
        },
        // Vuex 数据更改方法
        // 在严格模式下使用非 mutations 修改数据会报错
        mutations:{
            // payload 是接收传入的数据, 没有数据传入可不写
            reducePrice:(state,payload) => {
                state.products.forEach(product=>{
                    product.price -= payload
                })
            }
        },
        // 异步的情况之下使用的方法
        actions:{
            // payload 是接收传入的数据, 没有数据传入可不写
            reduce:(context,payload)=>{
                setTimeout(function(){
                    // 将数据传入 mutations 方法
                    context.commit('reducePrice',payload)
                },2000)
            }
        }
    })
    ```

* 在 `main.js` 内配置 vuex
```js
// 引入 vuex 数据文件
import {store} from './store/store.js'
// 将数据装载如主 Vue 内
new Vue({
  store: store
)}
```

* 组件内使用数据
```js
computed:{
        products(){
            // 调用数据
            return this.$store.state.products
            // 获取数据的方法
            return this.$store.getters.saleProducts
            // 同时调用多个获取数据的方法
            ...mapGetters([
                'saleProducts',
                'otherFunction',
                '...'
            ])
        }
    },
methods:{
        reducePrice(data){
            // 调用 mutations 内的方法, data 为传入的值
            this.$store.commit('reducePrice',data)
            // 调用 actions 内的方法, data 为传入的值
            this.$store.dispatch('reduce',data)
            // 同时调用多个 actions 的方法
            ...mapActions([
                'reduce',
                'otherFunction',
                '...'
            ])
        }
    }
```

* 使用 `...mapActions 及 ...mapGetters` 时需要完成下面几步
    1. `npm install babel-preset-stage-2 --save-dev` 安装插件
    2. 在 `babel.config.js` 内的 `presets` 数组内添加一个数组 `["stage-2"]`
    3. 在使用时需引入
        * `import {mapGetters} from 'vuex'`
        * `import {mapActions} from 'vuex'`
