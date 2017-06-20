<template>
  <div>
    <div class="tile is-ancestor">
      <div class="tile is-parent is-vertical">
        <article class="tile is-child is-vertical box">

          <!-- select resource type -->
          <article class="tile is-child is-vertical">
            <div class="field has-addons">
              <div class="field">
                <p class="control has-icons-right">
                  <span class="select">
                    <select v-model="resourceType" required :disabled="loading">
                      <option value="" disabled selected hidden>
                        Select a resource type...</option>
                      <option v-for="res in supportedResourceTypes">
                        {{res}}</option>
                    </select>
                  </span>
                </p>
              </div>
            </div>
          </article>

          <!-- resourceType: policy -->
          <article v-if="resourceType === 'Policy'" class="tile is-child is-vertical">
            <label class="label">Select policy</label>
            <div class="field has-addons">
              <p v-if="policies.length === 0" class="control">
                <a class="button is-primary" @click="listPolicies()" :disabled="loading">
                  List policies
                </a>
              </p>

              <p v-if="policies.length === 0" class="control" :disabled="loading">
                <input class="input" type="text"
                placeholder="Or enter a policy name..."
                v-model="selectedPolicy">
              </p>

              <p v-if="policies.length > 0"
              class="control has-icons-right"
              :disabled="loading">
                <span class="select">
                  <select v-model="selectedPolicy" required>
                    <option value="" disabled selected hidden>
                      Select a policy...</option>
                    <option v-for="policy in policies">
                      {{policy}}
                    </option>
                  </select>
                </span>
              </p>

              <p class="control" :disabled="loading">
                <a class="button is-info" @click="searchDependencies()">
                  Confirm
                </a>
              </p>
            </div>

            <a class="button is-loading"></a>

            <a class="button is-primary is-outlined">
              Check
            </a>
          </article>

        </article>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      csrf: '',
      supportedResourceTypes: ['Policy'],
      resourceType: '',
      loading: false,

      policies: [],
      selectedPolicy: ''
    }
  },

  mounted: function () {
    this.$notify({
      title: 'Under construction',
      message: 'This page is not fully function yet!',
      type: 'warning'
    })
  },

  methods: {
    searchDependencies: function () {
      // route to appropriate parsing function
      switch (this.resourceType) {
        case 'Policy':
          this.searchPolicyDependencies()
          break

        default:
          this.$notify({
            title: 'Not supported',
            message: 'Resource type ' + this.resourceType + ' is not supported',
            type: 'warning'
          })
          break
      }
    },

    listPolicies: function () {
      // if page is loading, this is disabled
      if (this.loading === true) {
        return
      }
      this.loading = true
      this.selectedPolicy = ''

      // fetch list of policies and set csrf
      this.$http.get('/api/policy').then((response) => {
        this.policies = response.data.result
        this.csrf = response.headers['x-csrf-token']
        this.loading = false
      })
      .catch((error) => {
        this.$onError(error)
        this.loading = false
      })
    },

    searchPolicyDependencies: function () {

    }

  }
}
</script>

<style scoped>
  .button {
    margin: 5px 0 0;
  }

  .control .button {
    margin: inherit;
  }

  .fa-trash-o {
    color: red;
  }

  .fa-info {
    color: lightskyblue;
  }

  select:invalid {
    color: gray;
  }
</style>
