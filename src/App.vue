<template>
    <v-app>
        <div v-if="signedIn">
            <v-navigation-drawer app clipped fixed v-model="leftDrawer">
                <v-list>
                    <v-list-item to='/'>
                        <v-list-item-action><v-icon>mdi-view-dashboard</v-icon></v-list-item-action>
                        <v-list-item-content><v-list-item-title>Dashboard</v-list-item-title></v-list-item-content>
                    </v-list-item>
                    <v-list-item to='/about'>
                        <v-list-item-action><v-icon>mdi-cloud-question</v-icon></v-list-item-action>
                        <v-list-item-content><v-list-item-title>About</v-list-item-title></v-list-item-content>
                    </v-list-item>
                </v-list>
            </v-navigation-drawer>

            <v-app-bar app clipped-left clipped-right>
                <v-app-bar-nav-icon @click.stop="leftDrawer = !leftDrawer"></v-app-bar-nav-icon>
                <v-toolbar-title>The Dashboard</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-btn icon @click.stop="rightDrawer = !rightDrawer">
                    <v-icon>mdi-login-variant</v-icon>
                </v-btn>
            </v-app-bar>

            <v-content><router-view/></v-content>

            <v-navigation-drawer app clipped fixed v-model="rightDrawer" right>
                <v-list>
                    <v-list-item to="/signin">
                        <v-list-item-action><v-icon>mdi-login-variant</v-icon></v-list-item-action>
                        <v-list-item-content><v-list-item-title>ログアウト</v-list-item-title></v-list-item-content>
                    </v-list-item>
                </v-list>
            </v-navigation-drawer>

            <v-footer fixed app><span>Handson</span></v-footer>
        </div>
        <div v-else>
            <amplify-authenticator />
        </div>
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/MaterialDesign-Webfont/3.8.95/css/materialdesignicons.css">
    </v-app>
</template>

<script>
import {Auth} from 'aws-amplify'
import {AmplifyEventBus} from 'aws-amplify-vue'

export default {
    name: 'App'
,   data () {
        return {
      leftMiniVariant : false,
      leftDrawer      : false,
      rightDrawer     : false,
      drawer: null,
      signedIn: false,
      username: '',
      userid: ''
    }
  },
  async beforeCreate() {
    // Auth.currentAuthenticatedUser()でユーザ情報を取得する。
    // 取得できなければ認証ステータスをfalseに設定する
    try {
      const cognitoUser = await Auth.currentAuthenticatedUser()
      this.signedIn = true
      this.username = cognitoUser.username
    } catch (err) {
      this.signedIn = false
    }
    // 認証ステータスが変わった時に呼び出されるイベントを登録
    AmplifyEventBus.$on('authState', async  info => {
      if (info === 'signedIn') {
        const cognitoUser = await Auth.currentAuthenticatedUser()
        this.signedIn = true
        this.username = cognitoUser.username
      } else {
        this.signedIn = false
      }
    });
  }
};
</script>
