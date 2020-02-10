<!--
#
# Copyright (C) 2020 Nethesis S.r.l.
# http://www.nethesis.it - nethserver@nethesis.it
#
# This script is part of NethServer.
#
# NethServer is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License,
# or any later version.
#
# NethServer is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with NethServer.  If not, see COPYING.
#
-->

<template>
  <div>
    <h2>{{$t('dashboard.title')}}</h2>
    <!-- error message -->
    <div v-if="errorMessage" class="alert alert-danger alert-dismissable">
      <button type="button" class="close" @click="closeErrorMessage()" aria-label="Close">
        <span class="pficon pficon-close"></span>
      </button>
      <span class="pficon pficon-error-circle-o"></span>
      {{ errorMessage }}.
    </div>

    <div v-show="!uiLoaded" class="spinner spinner-lg"></div>
    <div v-show="uiLoaded">
      <div v-if="status.installed">
        <div class="row rowstats">
          <div class="content">
            <div class="stats-container col-xs-12 col-sm-6 col-md-4 col-lg-4">
              <span class="card-pf-utilization-card-details-count stats-count">{{status.version}}</span>
              <span class="card-pf-utilization-card-details-description stats-description">
                <span
                  class="card-pf-utilization-card-details-line-2 stats-text"
                >{{$t('dashboard.mssql_version')}}</span>
              </span>
            </div>
          </div>
        </div>
        <div class="row rowstats">
          <div class="content">
            <div class="stats-container col-xs-12 col-sm-6 col-md-4 col-lg-4">
              <span class="card-pf-utilization-card-details-count stats-count">
                <span
                  :class="status.isrunning ? 'fa fa-check text-success' : 'fa fa-times text-danger'"
                ></span>
              </span>
              <span class="card-pf-utilization-card-details-description stats-description">
                <span
                  class="card-pf-utilization-card-details-line-2 stats-text"
                >{{$t('dashboard.mssql_status')}}</span>
              </span>
            </div>
          </div>
        </div>
        <div class="row rowstats">
          <div class="content">
            <div class="stats-container col-xs-12 col-sm-6 col-md-4 col-lg-4">
              <span class="card-pf-utilization-card-details-count stats-count">{{status.databases_number}}</span>
              <span class="card-pf-utilization-card-details-description stats-description">
                <span
                  class="card-pf-utilization-card-details-line-2 stats-text"
                >{{status.databases_number == 1 ? $t('dashboard.database') : $t('dashboard.databases')}}</span>
              </span>
            </div>
          </div>
        </div>
      </div>
      <div v-else>
        <div class="blank-slate-pf" id>
          <div class="blank-slate-pf-icon">
            <span class="pficon pficon pficon-add-circle-o"></span>
          </div>
          <h1>{{$t('package_required')}}</h1>
          <p>{{$t('package_required_desc')}}.</p>
          <pre>mssql-server</pre>
          <div class="blank-slate-pf-main-action">
            <router-link to="/settings">
              <button class="btn btn-primary btn-lg">{{$t('install_package')}}</button>
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Dashboard",
  components: {
  },
  props: {
  },
  mounted() {
    this.getDashboardData()
  },
  data() {
    return {
      uiLoaded: false,
      errorMessage: null,
      status: {
        installed: false,
        version: null,
        isrunning: false,
        databases_number: 0
      }
    };
  },
  methods: {
    showErrorMessage(errorMessage, error) {
      console.error(errorMessage, error) /* eslint-disable-line no-console */
      this.errorMessage = errorMessage
    },
    closeErrorMessage() {
      this.errorMessage = null
    },
    getDashboardData() {
      var context = this;
      context.uiLoaded = false;
      nethserver.exec(
        ["nethserver-mssql/dashboard/read"],
        { action: "status" },
        null,
        function(success) {
          try {
            context.status = JSON.parse(success).status;
          } catch (e) {
            console.error(e);
          }
          context.uiLoaded = true;
        },
        function(error) {
          context.showErrorMessage(context.$i18n.t("dashboard.error_reading_mssql_status"), error);
        }
      );
    }
  }
};
</script>

<style>
.stats-description-small {
  float: left;
  overflow: hidden;
  text-overflow: ellipsis;
  font-size: 16px;
  font-weight: 300;
  line-height: 2;
}
.stats-text-small {
  line-height: 0.5;
}
.stats-count {
  font-size: 26px;
  font-weight: 300;
  margin-right: 10px;
  float: left;
  line-height: 1;
}
.rowstats {
  padding-left: 20px;
  padding-right: 20px;
}
.stats-text {
  margin-top: 10px !important;
  display: block;
}
.stats-description {
  float: left;
  line-height: 1;
}
.stats-container {
  padding: 20px !important;
  border-width: initial !important;
  border-style: none !important;
  border-color: initial !important;
  -o-border-image: initial !important;
  border-image: initial !important;
}
.stats-text {
  margin-top: 10px !important;
  display: block;
}
.stats-description {
  float: left;
  line-height: 1;
}
.stats-count {
  font-size: 26px;
  font-weight: 300;
  margin-right: 10px;
  float: left;
  line-height: 1;
}
.item {
  padding-top: 3px;
}
.space {
  margin-bottom: 25px;
}
</style>