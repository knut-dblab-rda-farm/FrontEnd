<template>
    <div class="reviewModalDetail">
        <div class="back"/>
        <div class="inner">
            <header>
                <p class="close" @click="$emit('closeModal', '모달 닫기')">X</p>
                <h2 class="title" v-on:click="navigateAuction(clickedData.auction_Id)">{{clickedData.auction_name}}</h2>
            </header>
            <!-- <p>{{clickedData.auction_Id}}</p> -->
            <img class="reviewImg" :src="`/auciton_review_images/${clickedData.review_img_name}.png`" 
            alt="auction 이미지" width="200" height="200">
            <p class="reviewContent">{{clickedData.consumer_review}}</p>
            <i v-for="star in clickedData.grade_point" :key="star" class="fa fa-star review-star"/>
            <Comment 
                :auction_id="clickedData.auction_Id"
                :clickedData="clickedData"/>
        </div>
        <hr/>
    </div>    
</template>

<script>
import Comment from '../../components/Comment/comment.vue';
import axios from 'axios';
export default {
    props: {
        reviewModalState: Number,
        clickedData: Object
    },
    data(){
        return {
            user: JSON.parse(localStorage.getItem("user")),
        }
    },
    mounted(){
        console.log(`/auciton_review_images/${this.clickedData.review_img_name}.png`);
        console.log(this.clickedData);
    },
    components: {
        Comment,
        // CommentInput
    },
    methods: {
        navigateAuction (auction_Id) {
            axios.get(`/api/auctionInfo/${auction_Id}`, {
                headers: {
                    TOKEN: this.user.token
                }
            }).then(res => {
                console.log(res.data);
                this.$router.push({name:'auction_detail', params: { id: auction_Id, auction: JSON.stringify(res.data) }});
            }).catch(err => {
                console.log(err); 
                // if(res.headers.token != "token"){     
                // 	alert("중복 로그인으로 인해 로그아웃되었습니다. 다시 로그인 해 주시기 바랍니다.");        
                // 	this.$store.commit('LOGOUT');
                // 	this.$router.push('/login');
                // }
            });
        },
    }
}
</script>

<style lang="scss" scoped>
*{
    color: #333;
}
.reviewModalDetail{
    position: fixed;
    top: 0;
    z-index: 100;
    width: 400px;
    height: 100vh;
    .back{
        position: absolute;
        z-index: 1;
        width: 100%;
        height: 100%;
        background: rgba($color: #000000, $alpha: 0.7);
    }
    .inner{
        overflow-y: scroll;
        padding: 20px;
        position: absolute;
        text-align: center;
        z-index: 100;
        width: 80%;
        height: 80%;
        left: 0; right: 0; top: -8%; bottom: 0;
        margin: auto;
        background-color: #fff;
        border-radius: 10px;
        .close{
            position: absolute;
            right: 0;
            width: 60px;
            height: 60px;
            color: #333;
            font-weight: 900;
            cursor: pointer;
        }
        .title{
            font-size: 25px;
            font-weight: 900;
            margin: 10px;
            padding-bottom: 10px;
            border-bottom: 1px solid rgb(232, 232, 232);
        }
        .reviewImg{
            // width: 80%;
        }
        .review-star{
            color: #FFC1AA;
        }
        .reviewContent{
            font-size: 15px;
            font-weight: 600;
            margin-bottom: 10px;
            line-height: 1.4;
        }
        .reviewComment{
            border-top: 1px solid rgb(232, 232, 232);
            // margin-top: 20px;
            padding-top: 20px;
        }
    }
}
@media screen and (max-width: 400px){
    .reviewModalDetail{
        width: 100%;
        .inner{
            .reviewImg{
                // width: 30%;
            }
            .reviewContent{
                font-weight: 600;
                margin-bottom: 20px;
            }
        }
    }
}
</style>    