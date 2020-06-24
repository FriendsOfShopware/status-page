<template>
  <div>
    <div class="intro">
      <div class="logo--holder">
        <a href="https://friendsofshopware.com" title="FreindsOfShopware">
          <img src="https://d33wubrfki0l68.cloudfront.net/ba0770ca3d1c8b5f3df42cf96d7f8f0461270737/ed8ae/images/frosh_rgb.svg" alt="FreindsOfShopware Logo">
        </a>
      </div>
    </div>
    <div class="content--wrapper">
      <div class="status--wrapper" v-if="loaded">
        <div class="global-status">
          <div class="alert--success" v-if="account && account.up_monitors === pagination.total">
            <h1>Alle Systeme verfügbar</h1>
          </div>
          <div class="alert--warning" v-if="account && account.down_monitors > 0">
            <h1>Einige Systeme nicht verfügbar</h1>
          </div>
          <div class="alert--error" v-else-if="account && account.down_monitors === pagination.total">
            <h1>Alle Systeme nicht verfügbar</h1>
          </div>
        </div>
        <ul>
          <li class="system--holder" v-for="monitor of monitors">
            <span class="system-name">{{monitor.friendly_name}}</span><span class="system-status" v-if="monitor.status === 2"><span class="system-status--point success"></span><span class="system-status--name success">Verfügbar</span></span><span class="system-status system-status--warning" v-else-if="monitor.status === 2"><span class="system-status--point warning"></span><span class="system-status--name warning">Ungeprüft</span></span><span class="system-status system-status--warning" v-else-if="monitor.status === 8"><span class="system-status--point warning"></span><span class="system-status--name warning">Nicht erreichbar</span></span><span class="system-status system-status--error" v-else-if="monitor.status === 9"><span class="system-status--point error"></span><span class="system-status--name error">Nicht verfügbar</span></span>
          </li>
        </ul>
      </div>
      <div class="loader" v-else>
        <div class="loading-bar"></div>
      </div>
      <div class="footer">
        <a href="https://friendsofshopware.com" title="Studionuca Webseite">Go to FriendsOfShopware Website</a>
      </div>
    </div>
  </div>
</template>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100;200;300;400;500;600;700&display=swap');

  * {
    margin: 0;
    padding: 0;
    font-family: 'Inter', sans-serif;
    box-sizing: border-box;
    -webkit-font-smoothing: antialiased;
    text-rendering: geometricPrecision;
  }

  .intro {
    display: flex;
    width: 100%;
    height: 300px;
    background-color: #1B1B1B;
    align-items: center;
    justify-content: center;
    margin-bottom: 60px;
  }

  .logo--holder img {
    width: 100%;
    max-width: 460px;
    padding-left: 40px;
    padding-right: 40px;
  }

  .content--wrapper {
    margin: 0 auto;
    max-width: 850px;
    padding-left: 40px;
    padding-right: 40px;
  }

  .alert--success,
  .alert--warning,
  .alert--error {
    display: flex;
    height: 55px;
    max-width: 100%;
    align-items: center;
    padding: 10px 20px;
    border: 1px solid rgba(0,0,0,0.1);
    border-radius: 4px;
    margin-bottom: 70px;
  }

  .alert--success h1,
  .alert--warning h1,
  .alert--error h1 {
    color: #FFFFFF;
    font-size: 18px;
    line-height: 18px;
  }

  .alert--success,
  .success {
    color: #2ecc71;
    background-color: #2ecc71;
  }
  .alert--warning,
  .warning {
    color: rgb(250, 163, 26);
    background-color: rgb(250, 163, 26);
  }
  .alert--error,
  .error {
    color: rgb(241, 88, 50);
    background-color: rgb(241, 88, 50);
  }

  ul {
    list-style: none;
    border: 1px solid #E8E8E8;
    border-radius: 4px;
  }

  li.system--holder {
    display: flex;
    padding: 20px;
    border-bottom: 1px solid #E8E8E8;
  }

  li.system--holder:last-of-type {
    border-bottom: none;
  }

  .system-name {
    font-size: 16px;
    font-weight: 500;
    color: #1B1B1B;
  }

  .system-status {
    display: flex;
    margin-left: auto;
    align-items: center;
  }

  .system-status--point {
    content: "";
    display: inline-block;
    margin-right: 6px;
    height: 8px;
    width: 8px;
    border-radius: 100%;
  }

  .system-status--name { background-color: initial; }

  .footer {
    display: flex;
    margin-top: 70px;
    padding-top: 20px;
    padding-bottom: 60px;
    border-top: 1px solid #E8E8E8;
  }

  .footer a {
    margin-left: auto;
    font-size: 14px;
    color: #1B1B1B;
    text-decoration: underline;
  }

  .loading-bar {
    height: 4px;
    background-color: black;
    animation: loader 3s ease-in-out;
  }

  @keyframes loader {
    from { width: 0; }
    to { width: 100%; }
  }
</style>

<script>
  import axios from "axios";
  const API_KEY = 'ur963012-08cf3926697d9e70e85b2bac';

  export default {
    data() {
      return {
        loaded: false,
        monitors: [],
        account: [],
        pagination: []
      }
    },

    // Fetches posts when the component is created.
    created() {
      axios.post(`https://api.uptimerobot.com/v2/getMonitors`, `api_key=${API_KEY}&all_time_uptime_ratio=1&custom_uptime_ratios=30`, {
        header: {
          'content-type': 'application/x-www-form-urlencoded',
        }
      })
      .then(response => {
        // JSON responses are automatically parsed.
        this.monitors = response.data.monitors
        this.pagination = response.data.pagination
        this.loaded = 1;
      })
      .catch(error => {
        console.log(error);
      });
      axios.post(`https://api.uptimerobot.com/v2/getAccountDetails`, `api_key=${API_KEY}`, {
        header: {
          'content-type': 'application/x-www-form-urlencoded',
        }
      })
        .then(response => {
          // JSON responses are automatically parsed.
          this.account = response.data.account
          this.loaded = 2
        })
        .catch(error => {
          console.log(error);
        });
    },
  }
</script>
