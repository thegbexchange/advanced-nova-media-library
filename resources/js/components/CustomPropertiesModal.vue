<template>
    <Modal
      :show="true"
      maxWidth="2xl"
      @modal-close="handleClose"
          :classWhitelist="[
            'flatpickr-current-month',
            'flatpickr-next-month',
            'flatpickr-prev-month',
            'flatpickr-weekday',
            'flatpickr-weekdays',
            'flatpickr-calendar',
            ]"
     >
        <card class="overflow-hidden">
            <form class="rounded-lg shadow-lg overflow-hidden w-action-fields"
                @submit.prevent="handleUpdate"
                autocomplete="off"
            >
                <div class="font-bold py-5 pl-5">
                  <p>{{ parentField.name }}</p>
                </div>

                <div v-for="field in fields" :key="field.attribute" class="action">
                    <component :is="'form-' + field.component" :field="field"/>
                </div>

                <div class="text-red-500 pl-5">
                  {{ errorMessage }}
                </div>

                <div class="bg-30 px-6 py-3 flex">
                    <div class="flex items-center ml-auto">
                        <Button
                          @click.prevent="handleClose"
                          variant="link"
                          :label="__('Cancel')"
                        />
                        <Button
                          :label="__('Update')"
                          variant="solid"
                          @click.prevent="handleUpdate"
                        />
                    </div>
                </div>
            </form>
        </card>
    </Modal>
</template>

<script>
  import { Button } from 'laravel-nova-ui'

  export default {
    components: {
      Button,
    },
    props: {
        fields: {
            type: Array,
            required: true,
        },
        parentField: {
          type: Object,
          required: true
        }
    },

    data() {
      return {
        formData: null,
        errorMessage: null,
      }
    },

    methods: {
        handleClose () {
          if (this.checkRequired()) {
            this.$emit('close')
          }
        },

        handleUpdate () {
          if (this.checkRequired()) {
            this.$emit('update', this.formData)
          }
        },

        checkRequired() {

          let preventClose = false;

          this.formData = new FormData()

          this.fields.forEach(field => field.fill(this.formData))

          this.fields.forEach(field => {
            if (field.required === true) {

              let foundValue = false;

              for (let [property, value] of this.formData.entries()) {

                if (field.attribute === property && value !== undefined && value.length > 0) foundValue = true;

              }

              if (!foundValue) {
                preventClose = true;

                this.errorMessage = 'Please complete the above required fields (*), before continuing';
              } else {
                this.errorMessage = null;
              }
            }
          });

          return !preventClose;

        }
    }
  }
</script>
