<template>
    <div >
      
      <v-app id="inspire" style="background: #f6f9fb; font-family:helvetica">
        <v-navigation-drawer  dense :value="navDrawerVal" app color='#ffffff' width='235' clippped >
            <v-list-item>
                <v-list-item-content class="ma-2 text-center">
                  <v-list-item-avatar size="112">
                      <img v-if="isLogin" style="border: 3px solid #eeeeee;" :src="`http://adminsql1/photos/${SIdatabank_userInfo.EmployeeID}.jpg`" @error="viewAltImage">
                  </v-list-item-avatar>

                  <v-list-item-title style="font-size:20px; color:#525252" class="mt-2">{{ SIdatabank_userInfo.EmployeeID }}</v-list-item-title>
                  <v-list-item-subtitle >{{ getUserLevel }}</v-list-item-subtitle>
                  <v-list-item-subtitle class="mb-3">{{ SIdatabank_userInfo.EmployeeName }}</v-list-item-subtitle>
                  
                  <v-divider></v-divider>
                </v-list-item-content>
            </v-list-item>            
                
      <v-list shaped dense>

        <template v-for="item in drawerItems">
          <v-list-group  v-show=" SIdatabank_userInfo.UserLevel == 1" v-if="item.children" :key="item.text" v-model="item.model">

            <!-- Main Item with Dropdown -->

            <template v-slot:activator>
              <v-list-item-action>
                <v-icon>{{ item.icon }}</v-icon>
              </v-list-item-action>

              <v-list-item-content >
                <v-list-item-title>{{ item.text }}</v-list-item-title>
              </v-list-item-content>
            </template>

            <!-- Main Item with Dropdown Child -->
            <v-list-item  v-for="(child, i) in item.children" :key="i" link @click="visitPage(child.name)">
              
                <!-- @click="visitPage(child.name)" -->

              <v-list-item-action>
                <v-icon></v-icon>
              </v-list-item-action>

              <v-list-item-content>
                <v-list-item-title>{{ child.text }}</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list-group>

          <!-- Main Item only -->

              <v-list-item v-else :key="item.text" link @click="visitPage(item.name)">
                <v-list-item-action>
                  <v-icon>{{ item.icon }}</v-icon>
                </v-list-item-action>

                <v-list-item-content>
                  <v-list-item-title>{{ item.text }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </template>
          </v-list>

      <!-- Footer -->

          <v-footer padless fixed min-height="10">
            <v-container>
              <span class="ma-2" style="font-size: 12px"> v1.0.0</span>
            </v-container>
          </v-footer>

        </v-navigation-drawer>   

            <v-app-bar v-if="isLogin" height='50' app color='#ffffff'>
              <v-app-bar-nav-icon @click="navDrawerHide(null)"></v-app-bar-nav-icon>
              <div class=" disable-select hidden-sm-and-down">
                <v-container>
                  <v-card-subtitle class='pt-0 pb-0 mb-0 ' style="font-size:20px; color: #525252">
                    {{getCompany}}
                  </v-card-subtitle>
                </v-container> 
              </div>

              <v-spacer></v-spacer>
                    <v-btn  @click="logout()" depressed>Logout<v-icon>mdi-logout-variant</v-icon> 
                    </v-btn>
            </v-app-bar>
        <router-view></router-view>
      </v-app>
  </div>
</template>
<script>
import Swal from 'sweetalert2'
import store from '@/store'
export default {
    data() {
      return {
        UserLevelName:[],
      navDrawerList: [],
      user:{},
      isConnected: false,
      drawerItems: [
      {
        icon: 'mdi-monitor-dashboard',
        color:'red',
        text: 'Dashboard',
        name: 'DashBoard',
      },
      {
        icon: 'mdi-database-search',
        text: 'Records',
        name:'Records'
      },
      {
        icon: "mdi-cog",
        text: "Maintenance",
        model: false,
        children: [
          {
            icon: "mdi-account",
            text: "Users",
            name: "UserMaintenance",
          },
          {
            text: "Classifications",
            name: "Classifications",
          },
          {
            text: "Extent of Injury",
            name: "extentofinjury",
          },
          {
            text: "Extent of Damaged",
            name: "extentofdamaged",
          },
          {
            text: "Level of Injury",
            name: "levelofinjury",
          },
          {
            text: "Parts Injured",
            name: "partsinjured",
          },
          {
            text: "Property Damaged",
            name: "propertydamaged",
          },
          
          {
            text: "Primary TME",
            name: "primarytme",
          },
          {
            text: "Secondary TME",
            name: "secondarytme",
          },
        ],
      },
    ],    
  }
  },
  metaInfo(){
    return{
        link: [
          { rel: 'icon', href: './favicon.ico' } 
      ]
    }
  },
  created() {
    this.getSafetyUsers()
  },
  computed:{
      getCompany(){
        let companyName = "" 
        if(this.SIdatabank_userInfo.CompanyCode === "00"){
          companyName = 'PV Tech. Safety Investigatory Databank'
        }
        else if (this.SIdatabank_userInfo.CompanyCode === "10"){
          companyName = 'SCAD Safety Investigatory Databank'
        }
        else if (this.SIdatabank_userInfo.CompanyCode === "20"){
          companyName = 'HTI Safety Investigatory Databank'
        }
        else if (this.SIdatabank_userInfo.CompanyCode === "30"){
          companyName = 'HRD Safety Investigatory Databank'
        }
        else if (this.SIdatabank_userInfo.CompanyCode === "40"){
          companyName = 'WuKong Safety Investigatory Databank'
        }
        else if (this.SIdatabank_userInfo.CompanyCode === "50"){
          companyName = 'Majestic Landscape Corporation Safety Investigatory Databank'
        }
        else if (this.SIdatabank_userInfo.CompanyCode === "60"){
          companyName = 'Majestics Energy Corporation Safety Investigatory Databank'
        } 
        else if (this.SIdatabank_userInfo.CompanyCode === "70"){
          companyName = 'Dream Best Academy and Learning Safety Investigatory Databank'
        }
        else {companyName = ""}
        return companyName
      },
      getUserLevel(){
        let userLevelName
          this.UserLevelName.forEach(rec=>{
          if(rec.EmployeeID == this.SIdatabank_userInfo.EmployeeID){
          userLevelName =  rec.UserLevels
          }
        })
        return userLevelName
      },
  },
  watch:{

  },
  methods: {
    
    getSafetyUsers(){
            this.axios.get(`${this.api}/getSafetyUsers/${this.SIdatabank_userInfo.CompanyCode}`).then(res=>{  
            this.UserLevelName = res.data 
          })
        },
    viewAltImage(event){
      event.target.src = "http://hrdapps48:3001/ftp/employee_pictures/unkown.png"
        },
      logout() {
            store.commit('CHANGE_USER_INFO', {})
            store.commit('CHANGE_USER_LOGGING', false)
            store.commit('CHANGE_NAVDRAWER', false)
            this.$router.push('/login')
              const Toast =  
            Swal.mixin({
            toast: true,
            position: 'top',
            timer: 3000,
            showConfirmButton: false
          })
          Toast.fire({
            icon: 'success',
            title: `Successfully logged out`
        })
      },
      visitPage(page) {
          this.$router.push(page).catch(() => {});
      },
    navDrawerHide(path) {
      if(!store.state.navDrawerVal) {
          store.commit('CHANGE_NAVDRAWER', true)
      } else {
          store.commit('CHANGE_NAVDRAWER', false)
      }
      if(path != null) {
        this.$router.push(path).catch(err => {
        if(err.name != 'NavigationDuplicated') {
        console.log(err)
          }
        })
      }
    }
  }
}

</script>
<style>
body {
  background-color: lightblue;
overflow:hidden;
}
.disable-select {
    user-select: none; /* supported by Chrome and Opera */
   -webkit-user-select: none; /* Safari */
   -khtml-user-select: none; /* Konqueror HTML */
   -moz-user-select: none; /* Firefox */
   -ms-user-select: none; /* Internet Explorer/Edge */
  }

</style>

