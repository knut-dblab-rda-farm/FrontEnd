<template>
<div class="signup-contain">
    <header class="welcome-header">
        <h1 class="welcome-header__title">회 원 가 입</h1>
    </header>
    <v-form ref="form" lazy-validation @submit.prevent="SignupForm">
        <v-row>
            <v-col cols="12">
            <v-text-field class="inutBox" v-model="name" label="이름*" :rules="name_rule" required></v-text-field>
            </v-col>
            <v-col cols="12">
                <v-text-field class="inutBox" v-model="email" label="이메일*" :rules="emailRules"
                    :disabled="state == 'ins' ? false : true" required></v-text-field>
            </v-col>

            <button class="login-form__btn_right" @click="existEmail()">이메일 중복 검사</button>
            <v-col cols="12">
                <v-text-field class="inutBox" v-model="passwd" label="비밀번호*" type="password" :rules="passwd_rule"></v-text-field>
            </v-col>
            <v-col cols="12">
                <v-text-field class="inutBox" v-model="passwd_chk" label="비밀번호 확인*" type="password" :rules="passwd_rule2">
                </v-text-field>
            </v-col>
            <SearchAddress :addressInfo="addressInfo" @searchAddressRes="searchAddressResult" v-if="this.urlState == 0"/>
            <Auth :info="info" @authRes="phonenumAuthResult"/>
        </v-row>
        <br/><br/>
        <button class="login-form__btn_right"
            @click="signup()">다음</button>
    </v-form>
</div>

</template>


<script>
import axios from "axios"
import Auth from '../../components/auth.vue';
import SearchAddress from '../../components/search_address.vue';
export default {
    name: 'SignupForm',
    components: {
        Auth,
        SearchAddress,
    },
    data() {
        return {
            addressInfo:{
                zipcode: '',
                address: '',
                c_detail_location: '',
                isConsumer: this.$route.fullPath == '/signup',
            },
            info: {
                checkUser: 'signup',
                name: null,
                email: null,
            },
            phonenumAuth: false,
            addressCheck: false,
            name: null,
            email: null,
            passwd: null,
            phonenum: null,
            zipcode: null,
            location: null,
            c_detail_location: null,
            dialog: false,
            state: 'ins',
            authList: [
                { name: '관리자', value: 'A' },
                { name: '일반 사용자', value: 'M' }
            ],
            email: '',
            emailRules: [
                v => !!v || '이메일은 필수 입력사항입니다.',
                v => /^[A-Za-z0-9_\.\-]+@[A-Za-z0-9\-]+\.[A-Za-z0-9\-]+/.test(v) || '유효하지 않은 형식의 E-mail 입니다.',
                // v => this.existEmail(v) || '중복된'
            ],
            name: '',
            name_rule: [
                v => !!v || '이름은 필수 입력사항입니다.',
                v => !(v && v.length >= 10) || '이름은 10자 이상 입력할 수 없습니다.',
                v => !/[~!@#$%^&*()_+|<>?:{}]/.test(v) || '이름에는 특수문자를 사용할 수 없습니다.'
            ],
            passwd: '',
            passwd_chk: '',
            passwd_rule: [
                v => this.state === 'ins' ? !!v || '패스워드는 필수 입력사항입니다.' : true,
                v => !(v && v.length >= 30) || '패스워드는 30자 이상 입력할 수 없습니다.',
            ],
            passwd_rule2: [
                v => this.state === 'ins' ? !!v || '패스워드는 필수 입력사항입니다.' : true,
                v => !(v && v.length >= 30) || '패스워드는 30자 이상 입력할 수 없습니다.',
                v => v === this.passwd || '패스워드가 일치하지 않습니다.'
            ],
            user_auth: '',
            user_auth_rule: [
                v => !!v || '권한은 필수 선택 사항입니다.'
            ],
            phonenum: '',
            phonenum_rule: [
                v => this.state === 'ins' ? !!v || '핸드폰 번호는 필수 입력사항입니다.' : true,
                v => !(v && v.length >= 
                12) || '핸드폰 번호는 12자 이상 입력할 수 업습니다.',
                v => /[0-9]/.test(v) || '숫자만 입력해주세요'
            ],
            // State가 0이면 일반 사용자 1이면 농가유저
            urlState: 0,
        }
    },
    mounted(){
        // 일반 사용자
        if(this.$route.fullPath === '/signup'){
            this.urlState = 0;
            console.log(this.urlState);
        // 농가 사용자
        }else if(this.$route.fullPath === '/signupFarm'){
            this.urlState = 1;
            console.log(this.urlState); 
        }
    },
    methods:{
        existEmail() {
            if(!this.email) return alert('유효하지 않은 형식의 E-mail 입니다.');
            this.$store.dispatch('existEmail', this.email);
        },
        searchAddressResult(event){
            console.log('event: ', event);
            this.zipcode = event.zipcode;
            this.location = event.address;
            this.c_detail_location = event.c_detail_location;
            if(this.zipcode == '' || this.zipcode == '우편번호: ' || this.location == '' || this.location == '주소: ' 
                || this.c_detail_location == '' || this.c_detail_location == null) return alert('주소를 모두 입력해주세요!');
            console.log('this.c_detail_location: ' + this.c_detail_location);
            this.addressCheck = true;
            alert("주소 입력이 완료되었습니다!");
        },
        phonenumAuthResult(event){
            // 인증번호 맞는지 검사하고 맞다면 비밀번호 변경창 띄우기
            console.log('event: ', event);
            if(event.authInput == event.phoneAuthNumber){
                this.phonenumAuth = true;
                this.phonenum = event.phonenum;
                alert("인증 완료되었습니다.");
            } else {
                alert("인증번호가 맞지 않습니다!");
            }
        },
        async signup() {
            let validate;
            await this.$refs.form.validate().then( res => {
                console.log('res: '+res);
                validate = res.valid
                console.log(res);
            }).catch(err => {
                console.log(err);
            });
            console.log(validate);
            console.log(this.$store.state.existEmail);
            if(!validate){
                console.log("다른 거 확인하길 바람");
            } else if (this.$store.state.existEmail) {
                alert('이메일 중복 확인을 완료해주세요!')
            } else if(this.urlState == 0 && !this.addressCheck){
                alert('주소 입력을 완료해주세요!')
            } else if(!this.phonenumAuth){
                alert('핸드폰 번호 인증을 완료해주세요!')
            } else if(this.urlState == 1){
                console.log('tsdfsdf: ' + this.name);
                this.$store.commit('FARM_INFO', {name: this.name,
                                                email: this.email,
                                                passwd: this.passwd,
                                                phonenum: this.phonenum,
                                                checkUser: 'farm'});
                this.$store.state.kindOfFarm == 1 ? this.$router.push({ name: 'farm_user_info' }) : this.$router.push({ name: 'farm_biz_info' });
            } else {
                console.log();
                axios.post('/api/signupConsumer', { c_name: this.name, c_email: this.email, 
                                                    c_passwd: this.passwd, c_phonenum: this.phonenum, 
                                                    c_zipcode: this.zipcode, c_location: this.location,
                                                    c_detail_location: this.c_detail_location,
                                                    })
                    .then(res => {
                        console.log(res);
                        if (res.data == 0) {
                            alert("회원가입 실패..")
                        } else {
                            alert("회원가입 성공..")
                            // main으로 넘기기
                            console.log("main으로!!");
                            console.log(res.data);

                            localStorage.setItem('expire', JSON.stringify(Date.now() + 86400000));
                            localStorage.setItem("user", JSON.stringify(res.data));
                            localStorage.setItem('checkUser', 'consumer');
                            localStorage.setItem('id', res.data.consumer_id);
                            this.$store.commit('TOKEN_SAVE', res.data.token);
                            this.$router.push({name: 'main'});
                        }
                    })
                    .catch(err => {
                        console.log(err);
                    });
            }},
        },
    }
</script>

<style lang="scss" scoped>
button{
    margin: auto;
    font-weight: 700;
    width: 100%;
    height: 50px;
    border-radius: 20px;
    background-color: #FFC1AA;
    cursor: pointer;
}
.signup-contain{
    width: 100%;
    height: 100vh;
    .auth-box{
        .auth-title{
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 20px;
            padding-left: 10px;
        }
        .auth{
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 20px;
            .auth-input{
                font-size: 15px;
                padding: 10px;
                width: 45vw;
                height: 50px;
            }
            .auth-complete-btn{
                width: 25vw;
                height: 40px;
            }
            .timer{
                width: 50px;
                font-size: 12px;
                color: rgb(174, 174, 174);
            }
        }
    }
}
.login-form__btn_right{
    width: 95%;
    margin-left: 10px;    
    margin-bottom: 20px;
}
.login-form__btn_right:nth-child(3){
    width: 90%;
    margin: auto;
}
.inutBox{
    background-color: #fff;
    width: 90%;
    margin: auto;
}
// .inutBox:nth-child(2){
//     margin-top: 20px;
// }
</style>