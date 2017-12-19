<template>
  <div class="info">
      <el-card class="box-card">
        <div id="content">
          <el-table v-if="contacts.length != 0"
            :data="contacts"
            height="700"
            border
            style="width: 100%">
            <el-table-column
            align="center"
              prop="name"
              label="Name"
              width="180">
            </el-table-column>
            <el-table-column
            align="center"
              prop="phone"
              label="Phone"
              width="180">
            </el-table-column>
            <el-table-column
            align="center"
              prop="qq"
              label="QQ">
            </el-table-column>
            <el-table-column
            align="center"
              prop="wechat"
              label="Wechat">
            </el-table-column>
            <el-table-column
            align="center"
              prop="email"
              label="Email">
            </el-table-column>
            <el-table-column 
            label="Operation"
            align="center">
            <template slot-scope="scope">
              <el-button
                @click="modifyContact(scope.row)" icon="el-icon-edit"></el-button>
                <el-popover
                  ref="popover5"
                  placement="top"
                  width="160"
                  v-model="visible">
                  <p>Are you sure you want to delete this contact?</p>
                  <div style="text-align: right; margin: 0">
                      <el-button type="text" @click="visible = false">no</el-button>
                      <el-button type="primary" @click="visible = false">yes</el-button>
                  </div>
                </el-popover>
                <el-button
                  type="danger"
                  @click="deleteContact(scope.row)"
                  icon="el-icon-delete">
                </el-button>
              </template>
            </el-table-column>
          </el-table>
          <h1 v-if="contacts.length==0">You don't seem to have any contact</h1>
        </div>
      </el-card>
  </div>
</template>

<script>
export default {
  name: "Info",
  data() {
    return {
      visible: false,
      contacts: [],
      openSearch: false,
      openmodifyform: false,
      rules: {
        name: [
          {
            required: true,
            message: "Please input contact'name. ",
            trigger: "blur"
          },
          { type: "string", pattern: "^[A-Za-z0-9]+$", trigger: "blur" }
        ],
        phone: [
          {
            required: true,
            message: "Please input phone number",
            trigger: "blur"
          },
          {
            type: "string",
            pattern: "^[0-9]{11}$",
            message: "phone not valid!",
            trigger: "blur"
          }
        ],
        qq: [
          {
            type: "string",
            pattern: "[1-9][0-9]{4,}",
            message: "qq not valid!",
            trigger: "blur"
          }
        ],
        wechat: [
          {
            type: "string",
            pattern: "^[A-Za-z0-9]+$",
            message: "wechat not valid!",
            trigger: "blur"
          }
        ],
        email: [
          {
            type: "email",
            pattern: "^w+([-+.]w+)*@w+([-.]w+)*.w+([-.]w+)*$",
            message: "email not valid!",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    deleteContact(contact) {
      this.$confirm(
        `This operation will delete the contacts forever,
                     are you sure you want to continue?`,
        "Warning",
        {
          confirmButtonText: "yes",
          cancelButtonText: "no",
          type: "warning",
          center: true
        }
      )
        .then(() => {
          axios
            .get("api/contacts/", {
              params: {
                id: contact.id,
                type: "delete"
              }
            })
            .then(res => {
              let index = this.contacts.indexOf(contact);
              if (index > -1) {
                this.contacts.splice(index, 1);
              }
              this.$message({
                type: "success",
                message: "contacts removed"
              });
            })
            .catch(error => {
              console.log(error);
              this.$message({
                type: "info",
                message: "unknown error"
              });
            });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "delete canceled"
          });
        });
    }
  },
  beforeCreate() {
      axios.get('api/contacts/', {
        params: {
              type: 'get'
        }
    }).then( res => {
      let data = res.data
      console.log(data)
      if(data.status == '0') {
        this.contacts = data;
        console.log(this.contacts)
      } else if(data.status == '-1') {
        this.$router.push('/');
        this.$message.error(data.msg)
      }
      setTimeout(()=>{this.$emit('loading', false)},500)
    }).catch((error)=>{
      console.log(error);
      this.$emit('loading', false);
    });
  }
};
</script>

<style scoped>
.box-card {
  background-color: antiquewhite;
  margin-top: 0%;
}

#content {
  height: 700px;
}
</style>
