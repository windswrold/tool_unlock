<template>


	<div id="app" class="view">
		<el-tabs v-model="activeName" @tab-click="handleClick">



			<el-tab-pane label="绑定" name="first">

				<div>
					<div class="input_view">

						<div class="input-suffix">
							<div style="color: red;">*</div>
							<div class="input-suffix-text—modile">
								手机号
							</div>
							<el-input class="custom-input-bg" v-model="modile" placeholder="请输入手机号"></el-input>
							<el-button style="color: transparent;background-color: transparent;border: 0;"
								v-loading.fullscreen.lock="loading">{{verifyBtn}}</el-button>
						</div>
						<div class="input-suffix m10">
							<div style="color: red;">*</div>
							<div class="input-suffix-text—modile">
								验证码
							</div>
							<el-input class="custom-input-bg" v-model="verifyCode" placeholder="请输入验证码">

							</el-input>
							<!-- <div class="input-suffix-text">
								发送验证码
							</div> -->
							<el-button @click="sendCode" type="primary"
								v-loading.fullscreen.lock="loading">{{verifyBtn}}</el-button>

						</div>
						<div class="input-suffix m10">
							<div style="color: red;">*</div>
							<div class="input-suffix-text—modile">
								地址
							</div>

							<el-input class="custom-input-bg" v-model="bindAdds" placeholder="请输入地址"></el-input>
							<el-button style="color: transparent;background-color: transparent;border: 0;"
								v-loading.fullscreen.lock="loading">{{verifyBtn}}</el-button>
						</div>
						<div style="margin-top: 25px;font-size: 15px;color: red;">
							警告⚠️：当该手机用户已绑定地址时，将会覆盖旧地址。请谨慎操作。
						</div>
						<el-button style="width: 100%;margin-top: 50px;margin-bottom: 100px;" @click="tapSubmit3"
							type="primary" v-loading.fullscreen.lock="loading">确定</el-button>
							
					</div>
				</div>

			</el-tab-pane>
		</el-tabs>



		<el-dialog :visible.sync="dialogVisible">
			<img width="100%" src="./assets/example.jpg" alt="">
		</el-dialog>
	</div>
</template>

<script>
	import axios from "axios";

	export default {
		name: 'app',
		data() {
			return {
				hostname: window.location.hostname,
				activeName: 'first',
				action: "https://apiv2.sr2.top/api/upload_oss",
				userIDText: "",
				nameText: "",
				idNumersText: "",
				fileList: [],
				file1: "",
				file2: "",
				file3: "",
				file4: "",
				loading: false,
				dialogVisible: false,
				bindReason: "",
				bindAdds: "",
				modile: "",
				verifyCode: "",
				verifyBtn: "发送验证码",
				isSend: false,
				countdown: 120,
				timer: null,
			};
		},
		methods: {

			handleClick(tab, event) {
				// 
			},

			handleSuccess1(response, file, fileList) {
				var res_code = response.res_code;
				if (res_code == 0) {
					var data = response.data;
					this.file1 = data;
				} else {
					var res_msg = response.res_msg;
					this.showMessage(res_msg, "warning");
					this.$refs.upload1.clearFiles();
				}
			},
			handleSuccess2(response, file, fileList) {
				var res_code = response.res_code;
				if (res_code == 0) {
					var data = response.data;
					this.file2 = data;
				} else {
					var res_msg = response.res_msg;
					this.showMessage(res_msg, "warning");
					this.$refs.upload2.clearFiles();
				}
			},
			handleSuccess3(response, file, fileList) {
				var res_code = response.res_code;
				if (res_code == 0) {
					var data = response.data;
					this.file3 = data;
				} else {
					var res_msg = response.res_msg;
					this.showMessage(res_msg, "warning");
					this.$refs.upload3.clearFiles();
				}
			},
			handleSuccess4(response, file, fileList) {
				var res_code = response.res_code;
				if (res_code == 0) {
					var data = response.data;
					this.file4 = data;
				} else {
					var res_msg = response.res_msg;
					this.showMessage(res_msg, "warning");
					this.$refs.upload4.clearFiles();
				}
			},

			showMessage(text, type) {
				this.$message({
					message: text,
					type: type
				});
			},

			seeDefault() {


				this.dialogVisible = true;

			},

			sendCode() {
				console.log("send" + this.isSend);
				var modile = this.modile;
				var newhost = "apiv2." + this.hostname.split(".")[1] + "." + this.hostname.split(".")[2];

				if (modile.length == 0) {
					this.showMessage("请输入手机号", "warning");
					return;
				}
				var url = "https://" + newhost + "/api/msgSms/unbindCode";
				// apiv2.sr2.top/api/msgSms/unbindCode";
				if (this.isSend == false) {
					this.isSend = true;
					this.timer = setInterval(() => {
						this.countdown--;
						this.verifyBtn = this.countdown;
						if (this.countdown <= 1) {
							this.isSend = false;
							this.verifyBtn = "发送验证码";
							this.countdown = 120;
							clearInterval(this.timer);
						}
					}, 1000);

					try {
						axios.post(
							url, {
								"mobile": modile,
								"phone_prefix": "86"
							}
						).then(resp => {
							console.log('res ', resp.data);
							this.loading = false;
							var response = resp.data;
							var res_code = response.res_code;
							if (res_code == 0) {} else {
								var res_msg = response.res_msg;
								this.showMessage(res_msg, "warning");
							}

						}).catch(err => {
							console.log(err);
							this.loading = false;
							this.showMessage("请稍后再试", "warning");
						});
					} catch (e) {
						console.log(e);
						this.loading = false;
						this.showMessage("请稍后再试", "warning");
					}
				}
			},
			tapSubmit2() {
				var newhost = "apiv2." + this.hostname.split(".")[1] + "." + this.hostname.split(".")[2];
				console.log("newhost " + newhost);
				var user_id = this.userIDText;
				var reason = this.bindReason;
				var modile = this.modile;
				var verifyCode = this.verifyCode;


				if (modile.length == 0) {
					this.showMessage("请输入手机号", "warning");
					return;
				}
				if (verifyCode.length == 0) {
					this.showMessage("请输入验证码", "warning");
					return;
				}
				if (reason.length == 0) {
					this.showMessage("请输入解绑原因", "warning");
					return;
				}

				var url = "https://" + newhost + "/api/userAddress/create";

				try {
					axios.post(
						url, {
							"mobile": modile,
							"verify_code": verifyCode,
							"reason": reason
						}
					).then(resp => {
						console.log('res ', resp.data);
						this.loading = false;
						var response = resp.data;
						var res_code = response.res_code;
						if (res_code == 0) {
							this.showMessage("提交成功，等待审核", "success");
							this.modile = "";
							this.verifyCode = "";
							this.bindReason = "";

						} else {
							var res_msg = response.res_msg;
							this.showMessage(res_msg, "warning");
						}

					}).catch(err => {
						console.log(err);
						this.loading = false;
						this.showMessage("请稍后再试", "warning");
					});
				} catch (e) {
					console.log(e);
					this.loading = false;
					this.showMessage("请稍后再试", "warning");
				}
			},

			tapSubmit() {

				var user_id = this.userIDText;
				var username = this.nameText;
				var self_num = this.idNumersText;
				var front_of_id_card = this.file1;
				var hand_of_id_card = this.file2;
				var proposer = this.file3;
				var back_id_card = this.file4;

				if (user_id.length == 0) {
					this.showMessage("请输入用户ID", "warning");
					return;
				}
				if (username.length == 0) {
					this.showMessage("请输入姓名", "warning");
					return;
				}
				if (self_num.length == 0) {
					this.showMessage("请输入身份证号", "warning");
					return;
				}
				if (front_of_id_card.length == 0) {
					this.showMessage("请上传身份证正面照片", "warning");
					return;
				}
				if (back_id_card.length == 0) {
					this.showMessage("请上传身份证背面照片", "warning");
					return;
				}
				if (hand_of_id_card.length == 0) {
					this.showMessage("请上传手持身份证照片", "warning");
					return;
				}
				if (proposer.length == 0) {
					this.showMessage("请上传解封申请书", "warning");
					return;
				}

				console.log("user_id " + user_id +
					" username " + username +
					" self_num " + self_num + " front_of_id_card" + front_of_id_card + "hand_of_id_card " +
					hand_of_id_card + " proposer" + proposer
				);

				console.log("back_id_card " + back_id_card);

				this.loading = true;
				var url = "https://apiv2.sr2.top/api/userProposer/create";
				var params = {
					"user_id": user_id,
					"username": username,
					"self_num": self_num,
					"front_of_id_card": front_of_id_card,
					"hand_of_id_card": hand_of_id_card,
					"proposer": proposer,
					"reverse_of_id_card": back_id_card,
				}

				try {
					axios.post(
						url,
						params
					).then(resp => {
						console.log('res ', resp.data);
						this.loading = false;
						var response = resp.data;
						var res_code = response.res_code;
						if (res_code == 0) {
							this.showMessage("提交成功，等待审核", "success");
						} else {
							var res_msg = response.res_msg;
							this.showMessage(res_msg, "warning");
						}

					}).catch(err => {
						console.log(err);
						this.loading = false;
						this.showMessage("请稍后再试", "warning");
					});
				} catch (e) {
					this.loading = false;
					this.showMessage("请稍后再试", "warning");
				}

			},

			tapSubmit3() {
				var newhost = "apiv2." + this.hostname.split(".")[1] + "." + this.hostname.split(".")[2];
				console.log("newhost " + newhost);
				var adds = this.bindAdds;
				var modile = this.modile;
				var verifyCode = this.verifyCode;


				if (modile.length == 0) {
					this.showMessage("请输入手机号", "warning");
					return;
				}
				if (verifyCode.length == 0) {
					this.showMessage("请输入验证码", "warning");
					return;
				}
				if (adds.length == 0) {
					this.showMessage("请输入地址", "warning");
					return;
				}

				const regex = /^0x[a-fA-F0-9]{40}$/;
				var state = regex.test(adds);
				if (state == false) {
					this.showMessage("请输入正确地址", "warning");
					return;
				}

				var url = "https://" + newhost + "/api/userAddress/changeBind";

				try {
					axios.post(
						url, {
							"mobile": modile,
							"verify_code": verifyCode,
							"address": adds
						}
					).then(resp => {
						console.log('res ', resp.data);
						this.loading = false;
						var response = resp.data;
						var res_code = response.res_code;
						if (res_code == 0) {
							this.showMessage("提交成功，等待审核", "success");
							this.modile = "";
							this.verifyCode = "";
							this.bindAdds = "";

						} else {
							var res_msg = response.res_msg;
							this.showMessage(res_msg, "warning");
						}

					}).catch(err => {
						console.log(err);
						this.loading = false;
						this.showMessage("请稍后再试", "warning");
					});
				} catch (e) {
					console.log(e);
					this.loading = false;
					this.showMessage("请稍后再试", "warning");
				}
			}


		}
	}
</script>

<style>
	.view {

		padding: 20px 20px 0px 20px;
	}

	.input_view {

		border-radius: 14px;
		background-color: white;
		padding: 15px;
	}

	.input-suffix {

		display: flex;
		justify-content: start;
		align-items: center;
	}

	.input-suffix-text {

		color: #313131;
		font-size: 15px;
		/* width: 80px; */
	}

	.input-suffix-text—modile {

		color: #313131;
		font-size: 15px;
		/* break-after: column; */
		width: 80px;
	}

	.custom-input-bg .el-input__inner {

		color: #000000;
		border: 0;
		font-size: 15px;
		background-color: transparent;
		/* width: 200px; */
	}

	.upload-view {

		/* display: flex; */
		/* justify-content: space-between; */
		/* align-items: center; */
	}

	.upload-view-text {

		color: #000000;
		font-size: 15px;
	}

	.upload-view-default {

		color: #0C53FF;
		font-size: 15px;
	}

	.upload-view-img {

		width: 80px;
		height: 80px;
	}

	.upload-view-btn {

		border: #0C53FF;
		border-radius: 15px;
		color: #0C53FF;
		border-width: 1;
		background-color: white;
	}

	.m10 {
		margin-top: 10px;
	}
</style>