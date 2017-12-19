<template>
  <div class="search">
      <el-dialog width="90%" title="Search Results" :visible.sync="openSearch">
      <h1 v-if="searchData.contacts.length==0">No search results found</h1>
      <el-table v-if="searchData.contacts.length != 0"
            :data="searchData.contacts"
            height="500px"
            border>
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
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Search',
  data() {
      return {
          searchData: {
            name: '',
            contacts: []
          },
      }
  },
  methods: {
      search() {
      this.$refs['search-form'].validate(valid => {
        if(valid) {
          this.searchData.contacts = [];
      let contacts = this.contacts;
      for(let contact of contacts) {
        if(contact.name.includes(this.searchData.name)) {
          this.searchData.contacts.push(contact);
        }
      }
      this.openSearch = true;
      this.searchData.name = '';
        }
      })
      this.$refs['search-form'].clearValidate()
    }
  }
}
</script>

<style scoped>

</style>
