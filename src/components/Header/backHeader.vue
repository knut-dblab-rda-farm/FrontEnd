<template>
    <!-- 컴포넌트로 나누기 -->
    <header class="header">
        <nav>
            <li class="navLi"><router-link to="/"><v-icon>mdi-home</v-icon></router-link></li>
            <li class="navLi">{{headerProps}}</li>
            <li class="navLi">
                <p class="nav__link" @click="goBack()"><i class="fas fa-chevron-left fa-2x"
                        aria-hidden="true"></i>
                </p>
            </li>
        </nav>
    </header>   
</template>

<script>
export default {
    props: {
        headerProps: String
    },
    methods: {
        goBack(){
            this.$router.go(-1);
        }
    }
}
</script>

<style lang="scss" scoped>
*{
    list-style: none;
}
i{
    font-size: 25px;
}
.header{
    position: relative;
    z-index: 10;
    width: 100%;
    height: 60px;
    font-weight: 700;
    background-color: #FFC1AA;
    nav{
        width: 400px;
        height: 100%;
        display: flex;
        justify-content: space-around;
        align-items: center;
        .navLi{
            cursor: pointer;
            font-size: 16px;
            font-weight: 700;
        }
        .navLi:nth-child(1){
            font-size: 23px;
            margin-right: 30px;
        }
        .navLi:nth-child(2){
            margin-right: 50px;
        }
    }
}
</style>