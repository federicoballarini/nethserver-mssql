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
    <h2>{{$t('settings.title')}}</h2>
    
    <!-- error message -->
    <div v-if="errorMessage" class="alert alert-danger alert-dismissable">
      <button type="button" class="close" @click="closeErrorMessage()" aria-label="Close">
        <span class="pficon pficon-close"></span>
      </button>
      <span class="pficon pficon-error-circle-o"></span>
      {{ errorMessage }}.
    </div>

    <div v-if="!uiLoaded" class="spinner spinner-lg"></div>
    <div v-if="uiLoaded">
      <form class="form-horizontal" v-on:submit.prevent="saveConfig()">
        <div class="row">
          <div class="col-lg-12">
            <div :class="['form-group margintop', errors.DefaultLocale.hasError ? 'has-error' : '']">
              <label class="col-sm-2 control-label">
                {{$t('settings.default_locale')}}
                <doc-info
                  :placement="'top'"
                  :title="$t('settings.default_locale')"
                  :chapter="'DefaultLocale'"
                  :inline="true"
                ></doc-info>
              </label>
              <div class="col-sm-5">
                <select
                  required
                  v-model="configuration.DefaultLocale"
                  class="combobox form-control"
                >
                  <option v-for="(t,i) in configuration.LocaleList" v-bind:key="i">{{t}}</option>
                </select>
                <span v-if="errors.DefaultLocale.hasError" class="help-block">{{$t('settings.not_valid_default_locale')}}</span>
              </div>
            </div>
            <div :class="['form-group', errors.DefaultTimezone.hasError ? 'has-error' : '']">
              <label class="col-sm-2 control-label">
                {{$t('settings.default_timezone')}}
                <doc-info
                  :placement="'top'"
                  :title="$t('settings.default_timezone')"
                  :chapter="'DefaultTimezone'"
                  :inline="true"
                ></doc-info>
              </label>
              <div class="col-sm-5">
                <select
                  required
                  v-model="configuration.DefaultTimezone"
                  class="combobox form-control"
                >
                  <option v-for="(t,i) in configuration.TimezonesList" v-bind:key="i">{{t}}</option>
                </select>
                <span v-if="errors.DefaultTimezone.hasError" class="help-block">{{$t('settings.not_valid_default_timezone')}}</span>
              </div>
            </div>
            <div :class="['form-group', errors.DefaultToolbarIconsSize.hasError ? 'has-error' : '']">
              <label class="col-sm-2 control-label">
                {{$t('settings.default_toolbar_icons_size')}}
                <doc-info
                  :placement="'top'"
                  :title="$t('settings.default_toolbar_icons_size')"
                  :chapter="'DefaultToolbarIconsSize'"
                  :inline="true"
                ></doc-info>
              </label>
              <div class="col-sm-5">
                <select
                  required
                  v-model="configuration.DefaultToolbarIconsSize"
                  class="combobox form-control"
                >
                  <option value="small">{{$t('settings.small')}}</option>
                  <option value="medium">{{$t('settings.medium')}}</option>
                  <option value="large">{{$t('settings.large')}}</option>
                </select>
                <span v-if="errors.DefaultToolbarIconsSize.hasError" class="help-block">{{$t('settings.not_valid_default_toolbar_icons_size')}}</span>
              </div>
            </div>
            
            <!-- advanced menu -->
            <legend class="fields-section-header-pf" aria-expanded="true">
              <span
                :class="['fa fa-angle-right field-section-toggle-pf', advanced ? 'fa-angle-down' : '']"
              ></span>
              <a
                class="field-section-toggle-pf"
                @click="toggleAdvancedMode()"
              >{{$t('settings.advanced_mode')}}</a>
            </legend>
            <div v-if="advanced">
              <div :class="['form-group margintop', errors.SmtpAuth.hasError ? 'has-error' : '']">
                <label class="col-sm-2 control-label">
                  {{$t('settings.smtp_auth')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.smtp_auth')"
                    :chapter="'SmtpAuth'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    v-model="configuration.SmtpAuth"
                    type="checkbox"
                    class="form-control"
                    true-value="enabled"
                    false-value="disabled"
                  >
                  <span v-if="errors.SmtpAuth.hasError" class="help-block">{{$t('settings.not_valid_smtp_auth')}}</span>
                </div>
              </div>
              <div :class="['form-group', errors.SmtpStarttls.hasError ? 'has-error' : '']">
                <label class="col-sm-2 control-label">
                  {{$t('settings.smtp_starttls')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.smtp_starttls')"
                    :chapter="'SmtpStarttls'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    v-model="configuration.SmtpStarttls"
                    type="checkbox"
                    class="form-control"
                    true-value="enabled"
                    false-value="disabled"
                  >
                  <span v-if="errors.SmtpStarttls.hasError" class="help-block">{{$t('settings.not_valid_smtp_starttls')}}</span>
                </div>
              </div>
              <div class="form-group">
                <label class="col-sm-2 control-label">
                  {{$t('settings.pbx_provider')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.pbx_provider')"
                    :chapter="'PbxProvider'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    type="text"
                    class="form-control"
                    v-model="configuration.PbxProvider"
                  >
                </div>
              </div>
              <div :class="['form-group', errors.PbxProviderNethvoiceWebrestUrl.hasError ? 'has-error' : '']">
                <label class="col-sm-2 control-label">
                  {{$t('settings.nethvoice_webrest_url')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.nethvoice_webrest_url')"
                    :chapter="'PbxProviderNethvoiceWebrestUrl'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    type="text"
                    class="form-control"
                    v-model="configuration.PbxProviderNethvoiceWebrestUrl"
                  >
                  <span v-if="errors.PbxProviderNethvoiceWebrestUrl.hasError" class="help-block">{{$t('settings.not_valid_nethvoice_webrest_url')}}</span>
                </div>
              </div>
              <div :class="['form-group', errors.MinMemory.hasError ? 'has-error' : '']">
                <label class="col-sm-2 control-label">
                  {{$t('settings.min_memory')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.min_memory')"
                    :chapter="'MinMemory'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    type="number"
                    class="form-control"
                    v-model="configuration.MinMemory"
                    required
                  >
                  <span v-if="errors.MinMemory.hasError" class="help-block">{{$t('settings.not_a_number_min')}}</span>
                </div>
              </div>
              <div :class="['form-group', errors.MaxMemory.hasError ? 'has-error' : '']">
                <label class="col-sm-2 control-label">
                  {{$t('settings.max_memory')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.max_memory')"
                    :chapter="'MaxMemory'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    type="number"
                    class="form-control"
                    v-model="configuration.MaxMemory"
                    required
                  >
                  <span v-if="errors.MaxMemory.hasError" class="help-block">{{$t('settings.not_a_number_max')}}</span>
                </div>
              </div>
              <div :class="['form-group', errors.PublicUrl.hasError ? 'has-error' : '']">
                <label class="col-sm-2 control-label">
                  {{$t('settings.public_url')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.public_url')"
                    :chapter="'PublicUrl'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    type="text"
                    class="form-control"
                    v-model="configuration.PublicUrl"
                    :placeholder="configuration.PlaceholderPublicUrl"
                  >
                  <span v-if="errors.PublicUrl.hasError" class="help-block">{{$t('settings.not_valid_public_url')}}</span>
                </div>
              </div>
              <div :class="['form-group', errors.DavServerUrl.hasError ? 'has-error' : '']">
                <label class="col-sm-2 control-label">
                  {{$t('settings.dav_server_url')}}
                  <doc-info
                    :placement="'top'"
                    :title="$t('settings.dav_server_url')"
                    :chapter="'DavServerUrl'"
                    :inline="true"
                  ></doc-info>
                </label>
                <div class="col-sm-5">
                  <input
                    type="text"
                    class="form-control"
                    v-model="configuration.DavServerUrl"
                    :placeholder="configuration.PlaceholderDavServerUrl"
                  >
                  <span v-if="errors.DavServerUrl.hasError" class="help-block">{{$t('settings.not_valid_dav_server_url')}}</span>
                </div>
              </div>
            </div>

            <!-- save button -->
            <div class="form-group">
              <label class="col-sm-2 control-label">
              </label>
              <div class="col-sm-5">
                <button 
                  class="btn btn-primary margintop" 
                  type="submit"
                >
                  {{$t('save')}}
                </button>
              </div>
            </div>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: "Settings",
  mounted() {
    this.getConfig()
  },
  data() {
    return {
      uiLoaded: false,
      errorMessage: null,
      configuration: {
        DefaultLocale: 'en_US',
        DefaultTimezone: 'Etc/UTC',
        DefaultToolbarIconsSize: 'medium',
        SmtpAuth: 'disabled',
        SmtpStarttls: 'disabled',
        PublicUrl: null,
        DavServerUrl: null,
        LocaleList: null,
        TimezonesList: null,
        PbxProvider: null,
        PbxProviderNethvoiceWebrestUrl: null,
        MinMemory: 512,
        MaxMemory: 1024,
        PlaceholderPublicUrl: null,
        PlaceholderDavServerUrl: null
      },
      errors: this.initErrors(),
      advanced: false
    }
  },
  methods: {
    showErrorMessage(errorMessage, error) {
      console.error(errorMessage, error) /* eslint-disable-line no-console */
      this.errorMessage = errorMessage;
    },
    closeErrorMessage() {
      this.errorMessage = null;
    },
    getConfig() {
      var context = this;
      context.uiLoaded = false;
      nethserver.exec(
        ["nethserver-webtop5/settings/read"],
        { action: "configuration" },
        null,
        function(success) {
          try {
            context.configuration = JSON.parse(success).configuration;
          } catch (e) {
            console.error(e);
          }
          context.uiLoaded = true;
        },
        function(error) {
          context.showErrorMessage(context.$i18n.t("settings.error_reading_webtop5_configuration"), error);
        }
      );
    },
    saveConfig() {
      var context = this;
      var settingsObj = {
        action: "edit",
        "configuration": {
          DefaultLocale: context.configuration.DefaultLocale,
          DefaultTimezone: context.configuration.DefaultTimezone,
          DefaultToolbarIconsSize: context.configuration.DefaultToolbarIconsSize,
          SmtpAuth: context.configuration.SmtpAuth,
          SmtpStarttls: context.configuration.SmtpStarttls,
          PublicUrl: context.configuration.PublicUrl,
          DavServerUrl: context.configuration.DavServerUrl,
          PbxProvider: context.configuration.PbxProvider,
          PbxProviderNethvoiceWebrestUrl: context.configuration.PbxProviderNethvoiceWebrestUrl,
          MinMemory: context.configuration.MinMemory,
          MaxMemory: context.configuration.MaxMemory
        }
      };
      context.loaders = true;
      context.errors = context.initErrors();
      nethserver.exec(
        ["nethserver-webtop5/settings/validate"],
        settingsObj,
        null,
        function(success) {
          context.loaders = false;
          
          nethserver.notifications.success = context.$i18n.t(
            "settings.settings_updated_ok"
          );
          
          nethserver.notifications.error = context.$i18n.t(
            "settings.settings_updated_error"
          );
          
          // update values
          nethserver.exec(
            ["nethserver-webtop5/settings/update"],
            settingsObj,
            function(stream) {
              console.info("nethserver-webtop5-update", stream);
            },
            function(success) {
              context.getConfig();
            },
            function(error, data) {
              console.error(error, data);
            },
            true //sudo
          );
        },
        function(error, data) {
          var errorData = {};
          context.loaders = false;
          context.errors = context.initErrors();
          try {
            errorData = JSON.parse(data);
            for (var e in errorData.attributes) {
              var attr = errorData.attributes[e];
              context.errors[attr.parameter].hasError = true;
              context.errors[attr.parameter].message = attr.error;
            }
          } catch (e) {
            console.error(e);
          }
        },
        true // sudo
      );
    },
    initErrors() {
      return {
        DefaultLocale: {
          hasError: false,
          message: ""
        },
        DefaultTimezone: {
          hasError: false,
          message: ""
        },
        DefaultToolbarIconsSize: {
          hasError: false,
          message: ""
        },
        SmtpAuth: {
          hasError: false,
          message: ""
        },
        SmtpStarttls: {
          hasError: false,
          message: ""
        },
        PublicUrl: {
          hasError: false,
          message: ""
        },
        DavServerUrl: {
          hasError: false,
          message: ""
        },
        PbxProviderNethvoiceWebrestUrl: {
          hasError: false,
          message: ""
        },
        MinMemory: {
          hasError: false,
          message: ""
        },
        MaxMemory: {
          hasError: false,
          message: ""
        }
      }
    },
    toggleAdvancedMode() {
      this.advanced = !this.advanced;
      this.$forceUpdate();
    }
  }
};
</script>

<style scoped>
.margintop {
  margin-top: 15px;
}
</style>