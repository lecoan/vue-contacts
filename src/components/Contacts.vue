<template>
  <div id="box">
    <el-container>
    <el-main>
      <h1>Contacts</h1>
      <contact-info></contact-info>
    </el-main>
    <el-aside width="200px">
      <div id="search-div">
        <el-popover
          ref="search"
          placement="left"
          width="400"
          trigger="click">
          <el-form :inline="true" ref="search-form" :model="searchData" label-width="80px" :rules="rules">
            <el-form-item label="name" prop="name">
                <el-input v-model="searchData.name"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="search">Search</el-button>
            </el-form-item>
          </el-form>
        </el-popover> 
        <el-button round type="primary" icon="el-icon-search" v-popover:search></el-button>
      </div>
      <div id="add-div">
        <el-tooltip class="item" effect="dark" content="Add new Contact" placement="top-start"> 
        <el-button round type="primary" icon="el-icon-plus" @click="addContact"></el-button>
        </el-tooltip>
      </div>    
      <div id="logout-div">
        <el-tooltip class="item" effect="dark" content="Logout" placement="top-start"> 
        <el-button round type="danger" icon="el-icon-upload2" @click="logout"></el-button>
        </el-tooltip>
      </div>
    </el-aside>
  </el-container>
</div>
</template>

<script>
import Info from '@/components/Info.vue'
let axios = require('axios');
export default {
  name: 'Contacts',
  components: {
    'contact-info': Info
  },
  data () {
    return {
      csrftoken: '',
    }
  },
  methods: {
    addContact() {
      // this.openmodifyform = true;
      //  this.newContact = {
      //           id: -1,
      //           name: '',
      //           phone: '',
      //           qq: '',
      //           wechat: '',
      //           email: ''
      //         }
      //TODO pass add event
    },
    logout() {
      this.$confirm('Do you want to logout?', 'Hit', {
          confirmButtonText: 'Yes',
          cancelButtonText: 'No',
          type: 'warning',
          center: true
        }).then(() => {
          axios.get('/api/logout/').then(res => {
            let data = res.data
            if(data.status !== '0') {
              console.log(data.msg);
            } else {
              this.$message('please login again')
              this.$router.push('/')
            }
          }).catch(error => {
            console.log(error)  
            this.$message({
              type: 'error',
              message: 'unknown error'
            })
          })
        });
    }
  },
  beforeCreate() {
    this.$emit('loading', true);
    this.csrftoken = this.$cookie.get('csrftoken')
    console.log('token:'+this.csrftoken)
    // TODO add loadfinish event
  }
}
</script>
<style scoped>
h1 {
  font-size: 40px
}

#search-div {
  margin-top: 100%
}

#add-div {
  margin-top: 100%;
}

#logout-div {
  margin-top: 100%;
}

</style>
