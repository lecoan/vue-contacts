<template>
  <div class="modify">
      <el-dialog :visible.sync="openform">
          <el-form ref="modify-form" :model="newContact" label-width="80px" :rules="rules">
            <el-form-item label="name" prop="name">
                <el-input v-model="newContact.name" :disabled="newContact.id!==-1"></el-input>
            </el-form-item>
            <el-form-item label="phone" prop="phone">
                <el-input v-model="newContact.phone"></el-input>
            </el-form-item>
            <el-form-item label="qq" prop="qq">
                <el-input v-model="newContact.qq"></el-input>
            </el-form-item>
            <el-form-item label="wechat" prop="wechat">
                <el-input v-model="newContact.wechat"></el-input>
            </el-form-item>
            <el-form-item label="email" prop="email">
                <el-input v-model="newContact.email"></el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="handleUpload()">Submit</el-button>
                <el-button @click="openform=false">Cancel</el-button>
            </el-form-item>
          </el-form>
        </el-dialog>
  </div>
</template>

<script>
export default {
  name: "Modify",
  data() {
    return {
      newContact: {
        id: -1,
        name: "",
        phone: "",
        qq: "",
        wechat: "",
        email: ""
      }
    };
  },
  methods: {
    dispath() {
      let type = "add";
      if (this.newContact.id != -1) {
        type = "modify";
      }
      let instance = axios.create({
        headers: { "X-CSRFToken": this.csrftoken }
      });
      instance
        .post("api/contacts/", {
          contact: this.newContact,
          type: type
        })
        .then(res => {
          let data = res.data;
          console.log(data);
          if (data.status != "0") {
            this.$message.warning(data.msg);
            return;
          }
          if (data.type == "add") {
            this.$message.success("add success!");
            this.contacts.push(data.data);
          } else {
            this.$message.success("modify success!");
            let index = this.contacts.indexOf(data.data);
            if (index > -1) {
              this.contacts[index] = data.data;
            }
          }
        })
        .catch(error => {
          console.log(error);
          this.$message.error("unknown error");
        });
      this.openform = false;
    },
    modifyContact(contact) {
      this.openmodifyform = true;
      this.newContact = contact;
    },
    handleUpload() {
      this.$refs['modify-form'].validate(valid => {
        if(valid) {
          this.dispath()
        } else {
          this.$message.warning('please finish your form first!')
        }   
      })
      this.$refs['modify-form'].clearValidate()
    }
  }
};
</script>

<style scoped>

</style>
