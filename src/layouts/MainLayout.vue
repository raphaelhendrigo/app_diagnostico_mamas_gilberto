<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-bar class="q-electron-drag electron-only">
        <q-space></q-space>

        <q-btn dense flat icon="minimize" @click="minimize"></q-btn>
        <q-btn dense flat icon="crop_square" @click="maximize"></q-btn>
        <q-btn dense flat icon="close" @click="closeApp"></q-btn>
      </q-bar>
      <q-toolbar>
        <q-toolbar-title>
          <div style="width:50px; float:left; ">
            <q-btn to="/" flat style="width:40px">
              <img
                style="display:block; margin-right:auto; margin: 5px; "
                width="40px"
                src="~assets/logo_fitarosa.png"
              />
            </q-btn>
          </div>
          <div
            style="width:50%; float:left; word-break: break-all; padding-top:10px;  "
          >
            App Diagnóstico Mamas
          </div>
        </q-toolbar-title>

        <div><q-btn icon="info" to="/sobre" flat /></div>
      </q-toolbar>
    </q-header>
    <q-footer elevated>
      <q-toolbar>
        <q-toolbar-title>
          <div class="absolute-center	">
            <img
              style="margin-top: 5px; "
              width="140px"
              src="~assets/riopomba.png"
            />
          </div>
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
export default {
  name: "MainLayout",
  methods: {
    minimize() {
      if (process.env.MODE === "electron") {
        this.$q.electron.remote.BrowserWindow.getFocusedWindow().minimize();
      }
    },

    maximize() {
      if (process.env.MODE === "electron") {
        const win = this.$q.electron.remote.BrowserWindow.getFocusedWindow();

        if (win.isMaximized()) {
          win.unmaximize();
        } else {
          win.maximize();
        }
      }
    },

    closeApp() {
      if (process.env.MODE === "electron") {
        this.$q.electron.remote.BrowserWindow.getFocusedWindow().close();
      }
    }
  }
};
</script>
