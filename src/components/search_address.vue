<template>
    <div class="auth-contain">
        <v-col cols="12">
            <div class="auth-box">
                <button class="auth-complete-btn" @click="search()">주소 검색</button>
                <input type="text" id="postcode" class="information-form__input" placeholder="우편번호">
                <input type="text" id="roadAddress" class="information-form__input" placeholder="도로명 주소"><br>
                <input type="text" id="detailAddress" class="information-form__input" placeholder="상세 주소" v-model="c_detail_location" v-if="this.addressInfo.isConsumer"><br>
            </div>
            <button class="auth-button" @click="$emit('searchAddressRes', {zipcode: this.zipcode, address: this.address, c_detail_location: this.c_detail_location})">주소 확인</button>
        </v-col>
    </div>
</template>

<script>
export default {
    props: {
        addressInfo: {
            zipcode: String,
            address: String,
            c_detail_location: String,
            isConsumer: Boolean,
        }
    },
    data() {
        return {
            user: JSON.parse(localStorage.getItem('user')),
            zipcode: "",
            address: "",
            c_detail_location: "",
        };
    },
    mounted() {
        if(this.user != null){
            this.zipcode = this.addressInfo.zipcode;
            this.address = this.addressInfo.address;
            this.c_detail_location = this.addressInfo.c_detail_location;
        }
        console.log('addressInfo: ' + this.addressInfo.zipcode);
        console.log('addressInfo: ' + this.addressInfo.address);
        console.log('addressInfo: ' + this.addressInfo.isConsumer);

        document.getElementById('postcode').value = '우편번호: '+ this.addressInfo.zipcode;
        document.getElementById("roadAddress").value = '주소: '+ this.addressInfo.address;
        if(this.addressInfo.isConsumer) document.getElementById("detailAddress").value = '상세 주소: '+ this.addressInfo.c_detail_location;
    },

    methods: {
        search() { //@click을 사용할 때 함수는 이렇게 작성해야 한다.
            new window.daum.Postcode({
                oncomplete: (data) => { //function이 아니라 => 로 바꿔야한다.
                    // 팝업에서 검색결과 항목을 클릭했을때 실행할 코드를 작성하는 부분.

                    // 도로명 주소의 노출 규칙에 따라 주소를 표시한다.
                    // 내려오는 변수가 값이 없는 경우엔 공백('')값을 가지므로, 이를 참고하여 분기 한다.
                    var roadAddr = data.roadAddress; // 도로명 주소 변수
                    var extraRoadAddr = ''; // 참고 항목 변수

                    // 법정동명이 있을 경우 추가한다. (법정리는 제외)
                    // 법정동의 경우 마지막 문자가 "동/로/가"로 끝난다.
                    if (data.bname !== '' && /[동|로|가]$/g.test(data.bname)) {
                        extraRoadAddr += data.bname;
                    }
                    // 건물명이 있고, 공동주택일 경우 추가한다.
                    if (data.buildingName !== '' && data.apartment === 'Y') {
                        extraRoadAddr += (extraRoadAddr !== '' ? ', ' + data.buildingName : data.buildingName);
                    }
                    // 표시할 참고항목이 있을 경우, 괄호까지 추가한 최종 문자열을 만든다.
                    if (extraRoadAddr !== '') {
                        extraRoadAddr = ' (' + extraRoadAddr + ')';
                    }

                    // 우편번호와 주소 정보를 해당 필드에 넣는다.
                    document.getElementById('postcode').value = '우편번호: '+ data.zonecode;
                    document.getElementById("roadAddress").value = '주소: '+ roadAddr;
                    this.zipcode = data.zonecode;
                    this.address = roadAddr;
                }
            }).open();
        },
    }
}
</script>
<style lang="scss" scoped>
button{
    width: 100%;
    height: 50px;
    background-color: #FFC1AA;
}
.auth-contain{
    // margin-top: 10px;
    width: 100%;
    .auth-box{
        input{
            padding: 10px;
            background-color: rgb(245, 245, 245);
            width: 100%;
            height: 50px;
        }
        button{
            margin-bottom: 10px;
        }
    }
}
.auth-complete-btn, .information-form__input, .auth-button{
    width: 95%;
    margin: 10px 10px;
    border-radius: 10px;
    font-weight: 700;
}
.information-form__input{
    width: 90%;
}
</style>