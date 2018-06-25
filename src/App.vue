<template>
  <div id="app">
    <header>
      <h1>線上序號、密碼產生器</h1>
    </header>
    <section>
      <div class="lottery-data">
        <div style="text-align:left">
          <label class="font-20">條件</label>
          <div style="height: 70vh;background-color:#F8F8F8;padding: 10px ;">
            <ul>
              <li>
                <span>組數(必填)：</span>
                <span><input type="number" class="lottery-no" v-model="group_num" min="1" max="3000"></span>
              </li>
              <li>
                <span>長度(必填)：</span>
                <span><input type="number" class="lottery-no" v-model="length_num" min="1" max="30"></span>
              </li>
              <li>
                <span>阿拉伯數字(0~9)</span>
                <span><input type="checkbox" class="check-box" v-model="num_cb"></span>
              </li>
              <li>
                <span>大寫英文(A~Z)</span>
                <span><input type="checkbox" class="check-box" v-model="big_en_cb"></span>
              </li>
              <li>
                <span>小寫英文(a~z)</span>
                <span><input type="checkbox" class="check-box" v-model="small_en_cb"></span>
              </li>
              <li>
                <span>特殊符號(!@#$%^&*)</span>
                <span><input type="checkbox" class="check-box" v-model="mark_cb"></span>
              </li>
              <li>
                <span>去除相似字型(去除 1, i, l, I, !, o, 0, O)</span>
                <span><input type="checkbox" class="check-box" v-model="rm_smfont_cb"></span>
              </li>
              <li>
                <span>固定開頭碼</span>
                <span><input type="text" class="lottery-no" v-model="start_code_cb"></span>
              </li>
              <li>
                <span>固定結尾碼</span>
                <span><input type="text" class="lottery-no" v-model="end_code_cb"></span>
              </li>
              
            </ul>
            
          </div>
          
        </div>
      </div>
      <div class="doing">
        <button type="button" class="lottery" v-on:click="genPwd()">產生</button>
      </div>
      <div class="winning">
        <div>
          <div class="margin-center font-20 float-l">列表</div>
          <textarea v-model="result_data"></textarea>
        </div>
      </div>
    </section>
    <footer>
      <div>Made with by <a class="author">Sun Kuo</a> on GitHub</div>
    </footer>
  </div>
</template>
<script>
export default {
  name: 'app',
  data () {
    return {
        group_num:10,
        length_num:6,
        num_cb:true,
        big_en_cb:true,
        small_en_cb:true,
        mark_cb: false,
        rm_smfont_cb:false,
        start_code_cb:'',
        end_code_cb:'',
        result_data:''
    }
  },
  methods : {
      genPwd : function(){;
          if (this.group_num == '' || this.length_num == '') {
            alert("組數及長度為必填");
            return;
          }
          let password = this.generatePassword();
          this.result_data = password;
      },
      generateRandomNum : function(max){
          let crypto = window.crypto || window.msCrypto;
          if (!crypto) {
            throw new Error('Unsupported browser.');
          }
          // http://stackoverflow.com/a/18230432
          let array = new Uint8Array(1);
          crypto.getRandomValues(array);
          let range = max + 1;
          let max_range = 256;
          if (array[0] >= Math.floor(max_range / range) * range){
            return this.generateRandomNum(max);
          }
          return (array[0] % range);
      },
      generatePassword : function(){
        let uppercase = "ABCDEFGHJIKMNOPQRSTUVWXYZ";
        let lowercase = "abcdefghjikmnopqrstuvwxyz";
        let numbers = "1234567890";
        let special = "!@#$%&*?";
        let candidates = '';
        //阿拉伯數字(0~9) 
        if (this.big_en_cb) {
          candidates += uppercase;
        }
        //大寫英文(A~Z)
        if (this.small_en_cb) {
          candidates += lowercase;
        }
        //小寫英文(a~z)
        if (this.num_cb) {
          candidates += numbers;
        }
        //特殊符號(!@#$%^&*)
        if (this.mark_cb) {
          candidates += special;
        }
        //去除相似字型
        if (this.rm_smfont_cb) {
          candidates = candidates.replace(/1|i|l|I|!|o|0|O/gi, '');
        }
        let password = [];

        //組數
        for(let i = 0; i < this.group_num ; i++){
          password[i] = '';
          //長度
          for (let j = 0; j < this.length_num - this.start_code_cb.length - this.end_code_cb.length; j++) {
            let randomNum = this.generateRandomNum(candidates.length);
            if(candidates.length == randomNum){
              randomNum --;
            }
            password[i] += candidates.substring(randomNum, randomNum + 1);
          }
          password[i] = this.start_code_cb + password[i] + this.end_code_cb;
        }
        
        return password.join("\n");
      }



  }

}
</script>



<style>
body {
  background-color:#e1eef6;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  position: relative;
  height: 100vh;
  color: #004e66;
  font-weight: 400;
}

section {
  text-align: center;
  display:flex;
  align-items:center;
  justify-content:space-between;
  padding: 30px 20px 0 20px;
}

footer {
  position: fixed;
  bottom: 0;
}
h1, h2 {
  font-weight: normal;
  margin-top:0px;
}

ul {
  list-style-type: none;
  padding: 0px;
  margin: 0px
}

li {
  margin-bottom: 5px;
}


a {
  color: #42b983;
}
.author {
  color: #fcbe32;
}


textarea {
  width:100%;
  height: 70vh;
  font-size: 28px;
  margin-top: 5px;
}

.lottery {
  width: 200px;
    height: 50px;
    border-radius: .3rem;
    background-color: #ff5f2e;
    color: #fff;
    font-size: 30px;
}
.reset {
  background-color: transparent;
  border-color: transparent;
  text-decoration: underline;
  cursor: pointer;
  font-size: 15px;
}

.lottery-data, .winning {
  flex: 0 1 40%;
}
.doing{
  flex: 0 1 20%
}

.gen-btn {
  border-radius: .3rem;
  background-color: #fcbe32;
  color: #fff;
  font-size: 15px;
}

.lottery-no {
  width: 100%;
  height: 24px;
  font-size: 20px;
}

.check-box {
  height: 24px;
  font-size: 20px;
}

.margin-center {
  margin:0 auto;
}

.font-20 {
  font-size:20px;
}

.float-l {
  float:left;
}
</style>
