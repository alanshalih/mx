<template>
  <div>
    <v-card v-if="app_enabled && loading == false">



      <!-- <v-tabs v-model="tab" background-color="primary" dark>
      <v-tab v-for="item in items" :key="item.id">
        {{ item.tab }}
      </v-tab>
      <v-btn  icon class="mt-1 ml-1">
        <v-icon>mdi-plus</v-icon>
      </v-btn>
    </v-tabs> -->

      <div class="v-tabs theme--dark">
        <div role="tablist"
          class="v-item-group theme--dark v-slide-group v-tabs-bar v-tabs-bar--is-mobile white--text purple darken-3"
          data-booted="true">
          <div class="v-slide-group__prev v-slide-group__prev--disabled">
            <!---->
          </div>
          <div class="v-slide-group__wrapper">
            <div class="v-slide-group__content v-tabs-bar__content">

              <div @click="tab = item.id" v-for="(item,index) in items" :key="item.id" tabindex="0"
                :class="{'v-tab--active' : item.id == tab}" aria-selected="false" role="tab" class="v-tab">
                <span>{{item.tab}}</span>
                <v-btn class="ml-3" x-small icon @click="close(item,index)">
                  <v-icon>mdi-close</v-icon>
                </v-btn>
              </div>

              <button v-if="licence_data.tab > items.length" @click="addTabs()" type="button"
                class="mt-1 ml-1 v-btn v-btn--flat v-btn--icon v-btn--round theme--dark v-size--default"><span
                  class="v-btn__content"><i aria-hidden="true"
                    class="v-icon notranslate mdi mdi-plus theme--dark"></i></span></button>
            </div>
          </div>
          <div class="v-slide-group__next v-slide-group__next--disabled">
            <!---->
          </div>
        </div>
      </div>




      <div class="v-window v-item-group theme--light v-tabs-items">
        <div class="v-window__container">

          <div class="v-window-item  item-hide" :class="{'item-visible' : tab == item.id}" v-for="(item,index) in items"
            :key="item.id">
            <v-toolbar dense>
              <!-- <v-app-bar-nav-icon></v-app-bar-nav-icon> -->
              <v-btn @click="item.menu = !item.menu" icon>
                <v-icon>mdi-menu</v-icon>
              </v-btn>

              <!-- <v-toolbar-title>{{item.tab}}</v-toolbar-title> -->



              <v-btn :text="!routeList(item)" :class="{'purple  white--text' : routeList(item) }"
                @click="item.menu = true;routeTo({tab_id : item.id, route : 'list'})">
                List
              </v-btn>
              <v-btn :text="!routeCampaign(item)" :class="{'purple  white--text' : routeCampaign(item) }"
                @click="item.menu = true;routeTo({tab_id : item.id, route : 'campaign'})">
                Campaign
              </v-btn>
              <v-btn :text="!routeSetting(item)" :class="{'purple white--text' : routeSetting(item) }"
                @click="item.menu = true;routeTo({tab_id : item.id, route : 'setting'})">
                Setting
              </v-btn>


              <v-spacer></v-spacer>
              <small> Version {{versi}}</small>

              <v-menu bottom left>
                <template close-on-click v-slot:activator="{ on, attrs }">
                  <v-btn icon v-bind="attrs" v-on="on">
                    <v-icon>mdi-dots-vertical</v-icon>
                  </v-btn>
                </template>

                <v-list>
                  <v-list-item @click="importFile(item,index)">
                    <v-list-item-title>Import Database</v-list-item-title>
                  </v-list-item>
                  <v-list-item @click="exportFile(item)">
                    <v-list-item-title>Export Database</v-list-item-title>
                  </v-list-item>
                  <v-list-item @click="Refresh(item,index)">
                    <v-list-item-title>Refresh</v-list-item-title>
                  </v-list-item>
                  <v-list-item @click="close(item,index)">
                    <v-list-item-title>Tutup Tab</v-list-item-title>
                  </v-list-item>
                </v-list>

              </v-menu>
            </v-toolbar>
            <v-row no-gutters>
              <v-col v-if="item.menu" :cols="item.menu ? 3 : 0">


                <div>
                  <list v-if="item.route == 'list'" @routeTo="routeTo" :tab="item"></list>
                  <listDetail v-if="item.route == 'list-detail'" @routeTo="routeTo" :tab="item"></listDetail>
                  <scrapeContact v-if="item.route == 'grabb-contact'" @routeTo="routeTo" :tab="item"></scrapeContact>
                  <Campaigns v-if="item.route == 'campaign'" @routeTo="routeTo" :tab="item"></Campaigns>
                  <CampaignCreate v-if="item.route == 'create-campaign'" @routeTo="routeTo" :tab="item">
                  </CampaignCreate>
                  <CampaignDetail v-if="item.route == 'campaign-detail'" @routeTo="routeTo" :tab="item">
                  </CampaignDetail>
                  <ImportContact v-if="item.route == 'import-contact'" @routeTo="routeTo" :tab="item"></ImportContact>
                   <DeleteContact v-if="item.route == 'delete-contact'" @routeTo="routeTo" :tab="item"></DeleteContact>
                  <Setting v-if="item.route == 'setting'" @routeTo="routeTo" :licence_data="licence_data" :tab="item">
                  </Setting>
                </div>
              </v-col>
              <v-col :cols="item.menu ? 9 : 12">
                <webview :preload="path" nodeintegration :id="item.id" src="https://web.whatsapp.com"
                  useragent="Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:77.0) Gecko/20100101 Firefox/77.0"
                  style="display:inline-flex; width:100%; height:calc(100vh - 100px)"
                  :partition="'persist:'+item.uniq_code+'-'+item.id">
                </webview>
              </v-col>
              {{willNavigate(item)}}
            </v-row>
          </div>
        </div>
      </div>



    </v-card>
    <div class="purple darken-4" style="height : 100vh;" v-if="app_enabled == false && loading == false">
      <v-container>
        <v-row>

          <v-col style="margin-top : 200px;" md="6" offset-md="3" lg="4" offset-lg="4">
            <v-card>
              <v-card-text>
                <v-alert v-if="error" type="error">
                  Kode Akses tidak diterima
                </v-alert>

                <div class="title">SELAMAT DATANG DI APLIKASI MAXGRABB</div>
                <p>silakan masukan Kode Akses yang terdapat pada Dashboard MaxGrabb</p>
                <v-text-field label="Kode Akses" class="" v-model="access_code"></v-text-field>
                <v-btn class="purple darken-2 white--text" @click="registerApp()">REGISTER</v-btn>
                <!-- <p class="mt-3">belum punya Kode Akses? </p> -->
                <v-btn class="ml-3" @click="buyMaxGrabb()" color="success">Dapatkan Kode Akses</v-btn>
              </v-card-text>
            </v-card>
          </v-col>

        </v-row>

      </v-container>
    </div>

    <div v-if="loading" style="display:flex;justify-content:center;align-items:center;height:600px;">
      <v-progress-circular :size="50" color="purple darken-3" indeterminate></v-progress-circular>

    </div>
  </div>
</template>
<script>
  const ipc = require('electron').ipcRenderer;
  const {
    shell
  } = require('electron');
  const {
    dialog
  } = require('electron').remote
  import {
    nativeImage,
    clipboard
  } from 'electron'
  const fs = require('fs');
  import list from './List'
  import listDetail from './ListDetail'
  import scrapeContact from './ScrapeContact'
  import Campaigns from './Campaigns'
  import CampaignCreate from './CampaignCreate'
  import axios from "axios"
  import CampaignDetail from './CampaignDetail'
  import Setting from './Setting'
  import ImportContact from './ImportContact'
  import DeleteContact from './DeleteContact'
  
  const isDev = require('electron-is-dev');
  const path = require('path');
  const getSourceDirectory = () => isDev ?
    path.join(process.cwd(), 'dist_electron') // or wherever your local build is compiled
    :
    __dirname; // asar location
  const shortid = require('shortid');
  const preload = path.join(getSourceDirectory(), '/preload.js');

const contextMenu = require('electron-context-menu');

// const webview = document.querySelector("#webview");
// contextMenu({
//   window: webview
// });

  export default {
    data() {
      return {
        failed_msg : [],
        tab: 'home',
        machineId: '',
        error: false,
        loading: true,
        versi: '',
        app_enabled: false,
        access_code: '',
        path: preload,
        webview: {},
        licence_data: {},
        items: [],
        queue_message: [],
        endpoint: "https://manage.wegrabb.com",
        view: {},
      }
    },
    components: {
      list,
      listDetail,
      scrapeContact,
      CampaignCreate,
      Campaigns,
      CampaignDetail,
      ImportContact,
      DeleteContact,
      Setting
    },

    mounted() {

      // if(isDev){
      //   this.endpoint = 'http://wegrabb.test'
      // }

      this.machineId = ipc.sendSync('machineId');

      this.versi = ipc.sendSync('version');

      var data = localStorage.getItem('access_data');
      if (data) {
        data = JSON.parse(data);
        if (data.access_code) {
          this.access_code = data.access_code;
          this.registerApp();
        }
      } else {
        this.loading = false;
      }


 







    },
    methods: {
      routeSetting(item) {
        if (['setting'].includes(item.route)) {
          return true;
        } else {
          return false;
        }
      },
      routeCampaign(item) {
        if (['create-campaign', 'campaign', 'campaign-detail'].includes(item.route)) {
          return true;
        } else {
          return false;
        }
      },
      routeList(item) {
        if (['list', 'list-detail', 'grabb-contact', 'import-contact','delete-contact'].includes(item.route)) {
          return true;
        } else {
          return false;
        }
      },
      buyMaxGrabb() {
        shell.openExternal("https://manage.wegrabb.com/checkout/maxgrabb")
      },
      initApp() {
        this.items = ipc.sendSync('tabs');


        setTimeout(() => {
          if (this.items.length > 0) {
            this.tab = 'tab-1'
            this.willNavigate(this.items[0]);

          }

          var webview = {};

          this.items.forEach(tab => {
            const webview = document.getElementById(tab.id);
            

        


            this.loadTrainingMessage(tab);
            contextMenu({
              window: webview,
              append: (params, browserWindow) => [],
            });
            
            webview.addEventListener('ipc-message', (event) => {

              if (event.channel == 'paste-image') {
                const web = document.getElementById(event.args[0].tab_id);
                const image = nativeImage.createFromDataURL(event.args[0].image_data_url)
                clipboard.writeImage(image);
                web.paste();
              }

                if (event.channel == 'wa-not-found') {
                     this.failed_msg.push(event.args[0])
                }

                if(event.channel == 'number-not-found')
                {
                  var message = event.args[0];
                  axios.post(this.endpoint+'/api/maxgrabb/register-phone',{warming_up : false, phone : message.phone}).then(response=>{})
                }


             

            });
            setTimeout(()=>{
              webview.reloadIgnoringCache();
            },100)

          });

          if (this.items.length == 0) {
            this.addTabs();

          }

        }, 1000)
      },
      registerApp() {
        this.loading = true;
        axios.post(this.endpoint + "/api/register-app", {
          machineId: this.machineId,
          access_code: this.access_code
        }).then(response => {
          if (response.data.id) {
            this.app_enabled = true;
            localStorage.setItem('access_data', JSON.stringify(response.data))
            this.licence_data = response.data;
            if (this.licence_data.api_enabled) {
              this.fetchApi();
            }
            this.initApp();
            this.loading = false;
          }
        }, error => {
          this.error = true;
          this.loading = false;
        })
      },
      fetchApi() {
        setTimeout(() => {
          this.triggerSending();
          this.uploadFailedMessage();
        }, 60 * 1000)

      },
      loadTrainingMessage(tab)
      {
        if(tab.warming_up)
        {
            clearInterval(tab.interval)
            tab.interval = setInterval(()=>{
              const webview = document.getElementById(tab.id);
              axios.post(this.endpoint+'/api/maxgrabb/training-message').then(response=>{
                var message = response.data;
                message.tab_id = tab.id;
                message.warming_up = true;
              webview.send("send-message", message);

              })
            },tab.training_message_delay*1000*60)
        }
      
      },
      uploadFailedMessage()
      {
        setInterval(()=>{
          if(this.failed_msg.length > 0)
          {
            axios.post(this.endpoint + "/api/maxgrabb/kirim", {
                access_code: this.licence_data.access_code,
                data : this.failed_msg
              }).then(response => {
                  this.failed_msg = [];
              })
          }
        },60*1000)
      },
      triggerSending() {
        this.sendMessage();
        var time = this.randomIntFromInterval(9, 16);
        setTimeout(() => {
          this.triggerSending();
        }, time * 1000)
      },
      randomIntFromInterval(min, max) { // min and max included 
        return Math.floor(Math.random() * (max - min + 1) + min);
      },
      sendMessage() {


        axios.post(this.endpoint + "/api/maxgrabb/message", {
          access_code: this.licence_data.access_code
        }).then(response => {
          if (response.data.length > 0) {
            response.data.forEach(item => {
              const webview = document.getElementById(item.tab_id);
              webview.send("send-message", item);
            })
          }
        })



      },
      willNavigate(tab) {

        if (!this.webview[tab.id]) {

          this.webview[tab.id] = document.getElementById(tab.id);
          if (this.webview[tab.id]) {
            this.webview[tab.id].addEventListener('new-window', async (e) => {
              const protocol = require('url').parse(e.url).protocol
              if (protocol === 'http:' || protocol === 'https:') {
                await shell.openExternal(e.url)
              }
            })
          }

        }

      },
      importFile(tab, index) {
        dialog.showOpenDialog({
          properties: ['openFile']
        }).then(async result => {
          var text = await fs.readFileSync(result.filePaths[0], 'utf8')
          ipc.sendSync('save-db', {
            id: tab.id,
            text: text
          });
          this.close(tab, index);

          this.addTabs(tab, index)







        }).catch(err => {
          console.log(err)
        })
      },
      exportFile(tab) {

        const content = ipc.sendSync('read-db', tab);



        dialog.showSaveDialog({}).then(result => {
          var filename = result.filePath;
          if (filename === undefined) {
            alert('the user clicked the btn but didn\'t created a file');
            return;
          }

          fs.writeFileSync(filename + '.json', content, (err) => {
            if (err) {
              alert('an error ocurred with file creation ' + err.message);
              return
            }
          })
        }).catch(err => {
          alert(err)
        })

      },
      routeTo(data) {
        var index_init = -1;
        var tab = this.items.find((item, index) => {
          if (item.id == data.tab_id) {
            index_init = index;
            return true;
          }

        });

        if (tab) {

          tab.route = data.route;
          if (data.params)
            tab.params = data.params;
        }
      },
      addTabs(tab, index) {
        if (index) {
          tab.menu = false;
          this.items.splice(index, 0, tab)
          ipc.sendSync('add-tabs', tab);

        } else {
          // add tab manual
          for (var i = 1; i <= (this.items.length + 1); i++) {
            var find = this.items.find(item => {
              return item.id == 'tab-' + i;
            })
            if (!find) {
              var data = {
                tab: 'Tab ' + i,
                id: 'tab-' + i,
                uniq_code: shortid.generate(),
                menu: true,
                route: 'setting',
                params: {}
              };
              this.items.push(data)
              this.tab = data.id;
              ipc.sendSync('add-tabs', data);
              setTimeout(() => {

                axios.post(this.endpoint + "/api/app-license/tabs", {
                  access_code: this.licence_data.access_code,
                  data: JSON.stringify(this.items)
                });

                const webview = document.getElementById(data.id);
                webview.addEventListener('ipc-message', (event) => {

                  if (event.channel == 'paste-image') {
                    const image = nativeImage.createFromDataURL(event.args[0].image_data_url)
                    clipboard.writeImage(image);
                    webview.paste();
                  }

                  if (event.channel == 'wa-not-found') {
                   this.failed_msg.push(event.args[0])
                  }

                });
              }, 3000)

              break;
            }

          }
        }

      },
      close(item, index) {
        this.items.splice(index, 1);
        ipc.sendSync('close-tab', {
          id: item.id
        });
        axios.post(this.endpoint + "/api/app-license/tabs", {
          access_code: this.licence_data.access_code,
          data: JSON.stringify(this.items)
        });
      },
      Refresh(tab) {
        const webview = document.getElementById(tab.id);
        webview.reloadIgnoringCache();
      },
      openDevTools(tab) {
        const webview = document.getElementById(tab.id);
        webview.openDevTools();
      }
    }
  }
</script>
<style>
  .item-hide {
    display: none;
    ;
  }

  .item-visible {
    display: block !important;
  }
</style>