<template>
  <div class="extended-forms">
    <card>
      <div class="col-12">
        <div class="row">
          <div class="col-lg-4"></div>
        </div>
        <h2>Trabalho de Criptografia</h2>
        <div class="row">
          <div class="col-md-6">
            <h4 class="title">Selecione o algoritmo de Cifragem/Decifragem</h4>
            <div class="row">
              <div class="col-md-6">
                <el-select
                  class="select-info"
                  size="large"
                  placeholder="Algoritmos"
                  v-model="selects.simple"
                >
                  <el-option
                    v-for="option in selects.countries"
                    class="select-info"
                    :value="option.value"
                    :label="option.label"
                    :key="option.label"
                  >
                  </el-option>
                </el-select>
              </div>
              <h4 style="margin-left: 15px" class="title">
                Selecione se deseja Cifragem/Decifragem
              </h4>
              <div style="margin-left: 1px" class="row">
                <div class="col-md-12">
                  <el-select
                    class="select-info"
                    size="large"
                    placeholder="Algoritmos"
                    v-model="select.simple"
                  >
                    <el-option
                      v-for="options in select.countries"
                      class="select-info"
                      :value="options.value"
                      :label="options.label"
                      :key="options.label"
                    >
                    </el-option>
                  </el-select>
                </div>
              </div>
            </div>
          </div>
        </div>
        <fg-input label="Texto Claro">
          <textarea
            v-model="textoClaro"
            class="form-control"
            placeholder="Preencha com o texto claro"
            rows="3"
          ></textarea>
        </fg-input>
        <fg-input label="Chave">
          <textarea
            style="width:250px;height:40px;"
            v-model="chave"
            class="form-control"
            placeholder="Preencha com a chave"
            rows="3"
          ></textarea>
        </fg-input>
        <div>
          <h2>Subir Arquivo</h2>
          <input type="file" id="file" ref="file" @change="onFileChange" />
        </div>
        <div>
          <button type="button" class="btn btn-default btn-outline" @click="sendAlgoritmo"
            >Enviar</button>
        </div>
      </div>
    </card>
    <!-- end card -->
  </div>
</template>
<script>
import {
  DatePicker,
  TimeSelect,
  Slider,
  Tag,
  Input,
  Select,
  Option,
} from "element-ui";
import {
  Progress as LProgress,
  Switch as LSwitch,
  Radio as LRadio,
  Checkbox as LCheckbox,
  FormGroupInput as FgInput,
} from "src/components/index";
import Button from "../../../components/Button.vue";
import axios from "axios";
export default {
  components: {
    FgInput,
    [DatePicker.name]: DatePicker,
    [TimeSelect.name]: TimeSelect,
    [Slider.name]: Slider,
    [Tag.name]: Tag,
    [Input.name]: Input,
    [Button.name]: Button,
    [Option.name]: Option,
    [Select.name]: Select,
    LSwitch,
    LProgress,
    LRadio,
    LCheckbox,
  },
  data() {
    return {
      textoClaro: "",
      chave: "",
      enabledRadio: "2",
      disabledRadio: "2",
      file: "",
      switches: {
        defaultOn: true,
        defaultOff: false,
        plainOn: true,
        plainOff: false,
        withIconsOn: true,
        withIconsOff: false,
      },
      sliders: {
        simple: 30,
        rangeSlider: [20, 50],
      },
      selects: {
        simple: "",
        countries: [
          { value: "aes", label: "AES" },
          { value: "des", label: "DES" },
          { value: "des3", label: "3DES" },
          { value: "idea", label: "IDEA" },
          { value: "blowfish", label: "Blowfish" },
          { value: "twofish", label: "Twofish" },
        ],
        multiple: "ARS",
      },
      select: {
        simple: "",
        countries: [
          { value: "decrypt", label: "Decifragem" },
          { value: "encrypt", label: "Cifragem" },
        ],
        multiple: "ARS",
      },
      tags: {
        dynamicTags: ["Tag 1", "Tag 2", "Tag 3"],
        inputVisible: false,
        inputValue: "",
      },
      pickerOptions1: {
        shortcuts: [
          {
            text: "Today",
            onClick(picker) {
              picker.$emit("pick", new Date());
            },
          },
          {
            text: "Yesterday",
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24);
              picker.$emit("pick", date);
            },
          },
          {
            text: "A week ago",
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", date);
            },
          },
        ],
      },
      datePicker: "",
      dateTimePicker: "",
      timePicker: "",
    };
  },
  methods: {
    onFileChange(e) {
      //var files = e.target.files || e.dataTransfer.files;
      this.file = this.$refs.file.files[0];
      //if (!files.length)
      // return;
      //this.createImage(files[0]);
    },
    createImage(file) {
      var image = new Image();
      var reader = new FileReader();
      var vm = this;

      reader.onload = (e) => {
        vm.image = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    removeImage: function (e) {
      this.image = "";
    },
    handleClose(tag) {
      this.tags.dynamicTags.splice(this.tags.dynamicTags.indexOf(tag), 1);
    },

    sendAlgoritmo() {
      console.log(this.selects.simple);
      console.log(this.select.simple);
      console.log(this.file);

      let formData = new FormData();
      formData.append("file", this.file);
      formData.append("type", this.selects.simple);
      formData.append("key", this.key);
      axios
        .post("http://25.4.122.232:8000/"+this.select.simple+"/txtfile", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then(function () {
          console.log("SUCCESS!!");
        })
        .catch(function () {
          console.log("FAILURE!!");
        });
    },

    showInput() {
      this.tags.inputVisible = true;
      this.$nextTick(() => {
        this.$refs.saveTagInput.$refs.input.focus();
      });
    },

    handleInputConfirm() {
      let inputValue = this.tags.inputValue;
      if (inputValue) {
        this.tags.dynamicTags.push(inputValue);
      }
      this.tags.inputVisible = false;
      this.tags.inputValue = "";
    },
  },
};
</script>
<style>
.extended-forms .el-select {
  width: 100%;
  margin-bottom: 30px;
}

.extended-forms .progress {
  margin-bottom: 30px;
}
</style>
'