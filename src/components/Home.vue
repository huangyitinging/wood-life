<template>
    <div>
        <Header></Header>
        <Alert></Alert>
        <router-view></router-view>
    </div>
</template>

<script>
import Header from './Header';
import Alert from './AlertMessage';

export default {
    components:{
        Header,
        Alert,
    },
}
</script>