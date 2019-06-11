<template>
    <div>
        <loading :active.sync="isLoading"></loading>
        <div class="gap-setting">
            <div class="my-5 row justify-content-center">
                <div class="col-md-6">
                    <table class="table">
                        <thead>
                            <th></th>
                            <th>品名</th>
                            <th>數量</th>
                            <th>單價</th>
                        </thead>
                        <tbody>
                            <tr v-for="item in cart.carts" :key="item.id">
                                <td class="align-middle">
                                    <button type="button" class="btn btn-outline-danger btn-sm" 
                                    @click="removerCartItem(item.id)">
                                    <i class="far fa-trash-alt"></i>
                                    </button>
                                </td>
                                <td class="align-middle">
                                    {{ item.product.title }}
                                    <div class="text-success" v-if="item.coupon">
                                    已套用優惠券
                                    </div>
                                </td>
                                <td class="align-middle">{{ item.qty }}/{{ item.product.unit }}</td>
                                <td class="align-middle text-right">{{ item.final_total }}</td>
                            </tr>
                        </tbody>
                        <tfoot>
                            <tr>
                            <td colspan="3" class="text-right">總計</td>
                            <td class="text-right">{{ cart.total }}</td>
                            </tr>
                            <tr v-if="cart.final_total !== cart.total">
                            <td colspan="3" class="text-right text-success">折扣價</td>
                            <td class="text-right text-success">{{ cart.final_total }}</td>
                            </tr>
                        </tfoot>
                    </table>

                    <div class="input-group mb-3 input-group-sm">
                        <input type="text" class="form-control" placeholder="請輸入優惠碼" v-model="coupon_code">
                        <div class="input-group-append">
                            <button class="btn btn-outline-secondary" type="button" @click="addCouponCode">
                            套用優惠碼
                            </button>
                        </div>
                    </div>

                    <div class="mb-3 text-right">
                        <button type="button" class="btn btn-success" @click="checkout">前往結帳</button>
                    </div>  
                </div>
            </div>
        </div>  
    </div>
</template>

<script>
import $ from 'jquery';

export default {
    data(){
        return{
            cart:[],
            isLoading: false,
            coupon_code: '',
        };
    },
    methods:{
        getCart(){
            const vm = this;
            const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`;
            vm.isLoading = true;
            this.$http.get(url).then((response) => {
                vm.cart = response.data.data;
                console.log(response);
                vm.isLoading = false;
            });   
        },
        removerCartItem(id){
            const vm = this;
            const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart/${id}`;
            vm.isLoading = true;
            this.$http.delete(url).then((response) => {
                vm.$bus.$emit('updateCart');
                vm.$bus.$emit('message:push', response.data.message,'warning');
                vm.isLoading = false;
                vm.getCart();
            });   
        },
        addCouponCode(){
            const vm = this;
            const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/coupon`;
            const coupon ={
                code: vm.coupon_code,
            };
            this.$http.post(url,{ data: coupon }).then((response) => {
                vm.getCart();
                vm.$bus.$emit('message:push', response.data.message,'warning');
            });     
        },
        checkout(){
            const vm = this;
            if(vm.cart.carts.length != 0){
                this.$router.push('/checkout');
            }else{
                vm.$bus.$emit('message:push','請先選購商品唷！','danger');
            };
        },
    },
    created(){
        this.getCart();
    },
}
</script>