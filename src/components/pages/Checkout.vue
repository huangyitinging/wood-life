<template>
    <div>
        <div class="gap-setting">
            <div class="my-5 row justify-content-center">
                <form class="col-md-6" @submit.prevent="createOrder">
                    <div class="form-group">
                    <label for="useremail">Email</label>
                    <input type="email" class="form-control" name="email" id="useremail"
                        :class="{'is-invalid': errors.has('email')}" v-model="form.user.email"
                        v-validate="'required'" placeholder="請輸入 Email" required>
                    <span class="text-danger" v-if="errors.has('email')">
                        {{ errors.first('email') }}
                    </span>
                    </div>
                
                    <div class="form-group">
                    <label for="username">收件人姓名</label>
                    <input type="text" class="form-control" name="name" id="username"
                        :class="{'is-invalid': errors.has('name')}"
                        v-model="form.user.name" v-validate="'required'" placeholder="輸入姓名">
                    <span class="text-danger" v-if="errors.has('name')">姓名必須輸入</span>
                    </div>
                
                    <div class="form-group">
                    <label for="usertel">收件人電話</label>
                    <input type="tel" class="form-control" id="usertel" v-model="form.user.tel" placeholder="請輸入電話">
                    </div>
                
                    <div class="form-group">
                    <label for="useraddress">收件人地址</label>
                    <input type="text" class="form-control" name="address" 
                        :class="{'is-invalid': errors.has('address')}"
                        id="useraddress" v-model="form.user.address"
                        placeholder="請輸入地址" v-validate="'required'">
                    <span class="text-danger" v-if="errors.has('address')">地址欄位不得留空</span>
                    </div>
                
                    <div class="form-group">
                    <label for="comment">留言</label>
                    <textarea name="" id="comment" class="form-control" cols="30" rows="10" v-model="form.message"></textarea>
                    </div>
                    <div class="text-right">
                    <button class="btn btn-success">送出訂單</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return{
            form:{
                user:{
                    name: '',
                    email: '',
                    tel: '',
                    address: '',
                },
                message: '',
            },
        };
    },
    methods: {
        createOrder(){
            const vm = this;
            const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/order`;
            this.$validator.validate().then((valid) => {
                if (valid) {
                    this.$http.post(url,{ data: vm.form }).then((response) => {
                        // 成功送出訂單的話
                        if(response.data.success){
                            vm.$bus.$emit('updateCart');
                            vm.$bus.$emit('message:push', response.data.message,'warning');
                            vm.$router.push(`checkout/${response.data.orderId}`);
                        };
                    });
                } else{
                    vm.$bus.$emit('updateCart');
                    vm.$bus.$emit('message:push', '欄位不完整','danger');
                };
            });
        },        
    },
}
</script>