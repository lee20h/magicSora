<template>
  <ul class="shadow">
    <div v-for="(bookData) in propsdata" :key="bookData.bid">
      <div v-if="bookData.isaccept" id="cs">
        <img id="bookimg" :src=bookData.filename :class="{selledBook : bookData.issell}">
        <li :class="{selledBook : bookData.issell}">
            <p>책 이름: {{ bookData.name }} </p>
            <p>저자: {{ bookData.auth }}</p>
            <p>출판사: {{ bookData.pub }}</p>
            <p>가격: {{ bookData.price | currency }}</p>
        </li>
        <p v-if="bookData.issell">판매 완료</p>
        <p>
          <button :class="{loggedOut: !loggedIn}" @click="bookDelete(bookData.bid)">
            삭제
          </button>
        </p>
        <p :class="{loggedOut: !loggedIn}">
          <button v-if="!bookData.issell" @click="sold(bookData.bid)">
            판매처리
          </button>
          <button v-if="bookData.issell"  @click="unsold(bookData.bid)">
            판매취소
          </button>
        </p>
      </div>
    </div>
  </ul>
</template>
<script>
import host from '../assets/iptable.json'
import axios from 'axios'

export default {
  props: ['propsdata', 'loggedIn'],
  filters: {
    // 천 단위 기호 출력 (https://bit.ly/2Pf14QT)
    currency: value => {
      if (!value) return ''
      return value.toFixed(0).replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, "$1,") 
    } 
  },
  data: function() {
    return {
      host: host
    }
  },
  methods: {
    bookDelete : async function(bid) {
      if(!this.loggedIn) {
        console.log("Access Denied! You need admin access");
        return;
      }
      let vueInstance = this;
      this.axios.get(this.host.host + '/admin/adminCheck', {
        params: {
          token: window.localStorage.getItem('token')
        }
      })
      .then(function(response) {
        vueInstance.axios.post(vueInstance.host.host + '/book/bookDelete', {
          bid : bid
        })
        .then(function(res) {
          vueInstance.$emit('refresh');
        })
        .catch(function(err) {
          console.log(err);
        })
      })
      .catch(function(err) {
        alert("정상적인 접근 경로가 아닙니다.\n페이지를 새로고침 후 다시 시도 해주세요.");
        console.log(err);
      })
      .then(await function() {
        vueInstance.$emit('refresh');
      })
    },
    sold : function(bid) {
      if(!this.loggedIn) {
        console.log("Access Denied! You need admin access");
        return;
      }
      let vm = this;

      axios.get(vm.host.host + '/admin/adminCheck', {
        params: {
          token: window.localStorage.getItem('token')
        }
      })
      .then(function(response) {
        axios.post(vm.host.host + '/book/Sell', {
          bid : bid
        })
        .then(function(response) {
          vm.$emit('refresh');
        })
        .catch(function(err) {
          console.log(err);
        })
      })
      .catch(function(err) {
        alert("정상적인 접근 경로가 아닙니다.\n페이지를 새로고침 후 다시 시도 해주세요.");
        console.log(err);
      })
      .then(function() {
        vm.$emit('refresh');
      }) 
    },
    unsold : function(bid) {
      if(!this.loggedIn) {
        console.log("Access Denied! You need admin access");
        return;
      }
      let vm = this;
      axios.get(host.host + '/admin/adminCheck', {
        params: {
          token: window.localStorage.getItem('token')
        }
      })
      .then(function(response) {
        axios.post(vm.host.host + '/book/Sell_false', {
          bid : bid
        })
        .then(function(response) {
          vm.$emit('refresh');
        })
        .catch(function(err) {
          console.log(err);
        })
      })
      .catch(function(err) {
        alert("정상적인 접근 경로가 아닙니다.\n페이지를 새로고침 후 다시 시도 해주세요.");
        console.log(err);
      })
      .then(function() {
        vm.$emit('refresh');
      })
    }
  }
}
</script>

<style scoped>
  ul{
    grid-template-columns: 0.4fr 0.5fr;
    padding-left: 0;
    padding : 2%;
    list-style-type: none;
    background: white;
  }
  #cs {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 5px;
    /* margin: 15px; */
  }
  li {
    font-size: 1.0em;
    line-height: 32.5px;
    text-align: left;
    padding-left: 5%;
    padding-right: 10%;
  }
  #bookimg {
    max-width: 100%;
    width: 10rem;
    height: auto !important;
  }
  @media(max-width: 768px) {
    #cs {
      grid-template-rows: 200px;
    }
    li {
      font-size: 0.9rem;      
      line-height: 50px;
    }
    ul {
      grid-template-columns: 0.7fr 0.7fr;
    }
  }
  @media(max-width: 425px) {
    li {
      font-size: 0.7rem;
      line-height: 32.5px;
    }
    ul {
      grid-template-columns: 1.3fr 1fr;
      grid-template-rows: 200px;
    }
  }
  @media(max-width: 375px) {
    li {
      padding-left: 7%;
      font-size: 0.7rem;
      line-height: 32.5px;
    }
    ul {
      grid-template-columns: 1.3fr 1fr;
      grid-template-rows: 150px;
    }
  }
  .removeBtn {
    margin-left: auto;
    color: #de4343;
  }
  .checkBtn {
    line-height: 45px;
    color: #62acde;
    margin-right: 5px;
  }
  .checkBtnCompleted {
    color: #b3adad;
  }
  .textCompleted {
    text-decoration: line-through;
    color: #b3adad;
  }
  /* ListItem Transition Effect */
    .list-enter-active, .list-leave-active {
    transition: all 1s;
    }
    .list-enter, .list-leave-to /* .list-leave-active below version 2.1.8 */ {
    opacity: 0;
    transform: translateY(30px);
    }
.bookImgContainer {
  float: right;
  background: linear-gradient(to right, #6478fb, #8763fb);
  display: block;
  width: 3rem;
  border-radius: 0 5px 5px 0;
}
.loggedOut {
  display: none;
  /* visibility: hidden; */
}
.selledBook {
  color: gray;
  text-decoration: line-through;
  opacity: 0.5;
  /* background-color: lightblue; */
}
</style>