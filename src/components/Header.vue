<template>
    <header>
        <div class="regular-size">
            <router-link class="h-logo" to="/">
                <img src="../assets/images/logo.png" alt="">
            </router-link>
            <nav class="h-nav">
                <ul>
                    <li>
                        <router-link class="h-link" to="/list">商品列表</router-link>
                    </li>
                    <li>
                        <router-link class="h-link" to="/product">我的訂單</router-link>
                    </li>
                </ul>
            </nav>
            <div class="h-side">
                <router-link class="features cart" to="/shopcart">
                    <span class="counter" v-if="cart.length">{{ cart.length }}</span>
                    <img src="../assets/images/icon-cart.png" alt="">
                </router-link>
                <router-link class="features" to="/login">
                    <img src="../assets/images/icon-settings.png" alt="">
                </router-link>
            </div>
        </div>
    </header>
</template>

<script>
export default {
    data(){
        return{
            cart: [],
        };
    },
    methods:{
        getCart(){
            const vm = this;
            const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`;
            vm.isLoading = true;
            this.$http.get(url).then((response) => {
                vm.cart = response.data.data.carts;
                vm.isLoading = false;
            });   
        },        
    },
    created(){
        const vm = this;
        // 這裡是為了拿到購物車內的數量
        vm.getCart();
        // 註冊「updateCart」方法，讓子元件可以觸發
        vm.$bus.$on('updateCart', () => {
            vm.getCart();
        });
    },
}
</script>