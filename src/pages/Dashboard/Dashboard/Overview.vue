<template>
	<div class="extended-forms">
		<card>
			<div class="col-12">
				<h2>Trabalho de Criptografia</h2>
				<div class="row">
					<div class="col-md-6">
						<h4 class="title">Selecione o algoritmo de Cifragem/Decifragem</h4>
						<div class="col-md-6">
							<el-select
								class="select-info"
								size="large"
								placeholder="Algoritmos"
								v-model="algorithmType">
									<el-option
										v-for="option in algorithmsTypes"
										class="select-info"
										:value="option.value"
										:label="option.label"
										:key="option.label"/>
							</el-select>
						</div>
						<h4 style="margin-left: 1px" class="title">
							Selecione se deseja Cifragem/Decifragem
						</h4>
						<div style="margin-left: 1px" class="row">
							<div class="col-md-6">
								<el-select
								class="select-info"
								size="large"
								placeholder="Algoritmos"
								v-model="opType">
									<el-option
										v-for="options in opTypes"
										class="select-info"
										:value="options.value"
										:label="options.label"
										:key="options.label"/>
								</el-select>
							</div>
						</div>
					</div>
				</div>
				<fg-input label="Texto Claro/Cifrado">
					<textarea
						v-model="textoClaro"
						class="form-control"
						placeholder="Preencha com o texto claro ou cifrado"
						rows="3"/>
				</fg-input>
				<fg-input label="Chave">
					<textarea
						style="width: 250px; height: 40px"
						v-model="chave"
						class="form-control"
						placeholder="Preencha com a chave"
						rows="3"/>
				</fg-input>
				<div>
					<h2>Subir Arquivo</h2>
					<input
						style="margin-top: 15px; border-color: #23ccef"
						class="btn btn-info btn-outline"
						type="file"
						id="file"
						ref="file"
						@change="onFileChange"
					/>
				</div>
				<div>
					<Button
						style="margin-top: 15px; border-color: #23ccef"
						stype="button"
						class="btn btn-info btn-outline"
						@click="sendAlgoritmo">
						Enviar
					</Button>
				</div>
			</div>
		</card>
	</div>
</template>
<script>
	import {
		Select,
		Option,
	} from "element-ui";
	import Button from "../../../components/Button.vue";
	import axios from "axios";
	import Swal from 'sweetalert2'

	export default {
		components: {
			[Button.name]: Button,
			[Option.name]: Option,
			[Select.name]: Select,
		},
		data() {
			return {
				textoClaro: "",
				chave: "",
				file: "",
				algorithmType:"",
				opType:"",
				algorithmsTypes: [
					{ value: "aes", label: "AES" },
					{ value: "des", label: "DES" },
					{ value: "des3", label: "3DES" },
					{ value: "idea", label: "IDEA" },
					{ value: "blowfish", label: "Blowfish" },
					{ value: "twofish", label: "Twofish" },
				],
				opTypes: [
					{ value: "encrypt", label: "Cifragem" },
					{ value: "decrypt", label: "Decifragem" },
				],
			};
		},
		methods: {
			onFileChange() {
				this.file = this.$refs.file.files[0];
			},
			textRequest(){
				let operationType = {
					"encrypt": "cifrada",
					"decrypt": "decifrada"
				};
				let variableType = {
					"encrypt": "encrypted_message",
					"decrypt": "decrypted_message"
				}
				console.log({
					"message": this.textoClaro,
					"key": this.chave,
					"key_size": 128
				})
				
				axios.post(
					"http://localhost:8000/"+this.opType+"/"+this.algorithmType, 
					{
						"message": this.textoClaro,
						"key": this.chave,
						"key_size": 128
					}
				)
				.then((response) => {
					this.showSwal(`Mensagem ${operationType[this.opType]} com sucesso !`, response.data[variableType[this.opType]], 'success');
					console.log(response.data);
				})
				.catch((error)=>{
					this.showSwal('Error !', error.message, 'danger');
					console.log(error.message);
				});
			},
			fileRequest() {
				let operationType = {
					"encrypt": "cifrada",
					"decrypt": "decifrada"
				};
				let variableType = {
					"encrypt": "encrypted_message",
					"decrypt": "decrypted_message"
				}
				let formData = new FormData();

				formData.append("file", this.file);
				formData.append("type", this.algorithmType);
				formData.append("key", this.chave);

				axios.post(
					"http://localhost:8000/" + this.opType + "/txtfile",
					formData,
					{
						headers: {
							"Content-Type": "multipart/form-data",
						},
					}
				)
				.then((response)=>{
					this.showSwal(`Mensagem ${operationType[this.opType]} com sucesso !`, response.data[variableType[this.opType]], 'success');
					console.log("SUCCESS!!");
					console.log(response.data)
				})
				.catch((error)=>{
					console.log("FAILURE!!");
					this.showSwal('Error !', error.message, 'danger');
					console.log(error.message);
				});
			},
			sendAlgoritmo() {
				if(!!this.file){
					this.fileRequest()
				}
				else{
					this.textRequest();
				}
				
			},
			showSwal(message, description, type) {
				Swal.fire(
					{
						title: message,
						text: description,
						buttonsStyling: false,
						confirmButtonClass: `btn btn-${type} btn-fill`,
						type: 'success'
					}
				);
			}
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