<template>
  <v-dialog v-model="dialogEdit" max-width="500px">
    <v-form @submit.prevent="confirmEdit" id="UpdateClientForm">
      <v-card>

        <v-card-title>
          <span class="text-h5">Edit Client</span>
        </v-card-title>

        <v-card-text>
          <v-container>

            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="newClient.name"
                  name="name"
                  label="name"
                v-validate="'alpha_spaces|required'"
                ></v-text-field>
                <span class="validation-error">{{ errors.first('name') }}</span>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="newClient.email"
                  name="email"
                  label="email"
                  v-validate="'required|email'" 
                ></v-text-field>
                <span class="validation-error">{{ errors.first('email') }}</span>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="newClient.phone"
                  v-validate="{ required: true, regex:/^(00213|\+213|0)(5|6|7)[0-9]{8}$/ }"
                  name="phone"
                  label="phone"
                ></v-text-field>
                <span class="validation-error">{{ errors.first('phone') }}</span>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="newClient.address"
                  v-validate="'required'"
                  name="address"
                  label="address"
                ></v-text-field>
                <span class="validation-error">{{ errors.first('address') }}</span>
              </v-col>
            </v-row>

          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
          <v-btn type="submit" color="green darken-1" text form="UpdateClientForm">Save</v-btn>
        </v-card-actions>

      </v-card>
    </v-form>
  </v-dialog>
</template>
<script>
import axios from "axios";

export default {

  props: {
    dialogEdit: Boolean,
    current: Object,
    close: { type: Function }
  },

  data(){
    return {
       newClient:{},
    }
  },

  mounted(){
      this.setNewClient();
  },

  methods: {

    setNewClient(){
      this.newClient = Object.assign({}, this.newClient, this.current)
    },

    async confirmEdit() {
        this.$validator.validateAll().then(result => {
        if(result){
          new Promise((resolve, reject) => {
        axios
          .put(`clients/${this.current.id}`, this.newClient)
          .then(res => {
            this.$bus.emit("edit", this.client);
            this.close();
            resolve(res);
          })
          .catch(err => {
            console.log(err);
            this.close();
            reject(err);
          });
      });
        }
      })
    },

  }
};
</script>
<style scoped>
.validation-error{
  color:#f00;
}
</style>