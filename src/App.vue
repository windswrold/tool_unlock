<template>
	<div id="app" class="view">

		<div class="input_view">
			<div class="input-suffix">
				<div class="input-suffix-text">
					用户ID
				</div>
				<el-input class="custom-input-bg" v-model="userIDText" placeholder="请输入用户ID"></el-input>
			</div>

			<div class="input-suffix m10">
				<div class="input-suffix-text">
					姓名
				</div>
				<el-input class="custom-input-bg" v-model="nameText" placeholder="请输入姓名"></el-input>
			</div>

			<div class="input-suffix m10">
				<div class="input-suffix-text">
					身份号
				</div>
				<el-input class="custom-input-bg" v-model="idNumersText" placeholder="请输入身份证号"></el-input>
			</div>



		</div>

		<div class="input_view m10">
			<div class="upload-view-text">身份证照片</div>
			<div class="upload-view">
				<el-upload class="upload-view m10" :action="action" :limit="1" :file-list="fileList" ref="upload1"
					list-type="picture-card" :on-success="handleSuccess1">
					<img src="./assets/idcard.png" class="upload-view-img m10" />
				</el-upload>
			</div>
		</div>

		<div class="input_view m10">
			<div class="upload-view-text">手持身份证照片</div>
			<div class="upload-view">
				<el-upload class="upload-view m10" :action="action" :limit="1" :file-list="fileList" ref="upload2"
					list-type="picture-card" :on-success="handleSuccess2">
					<img src="./assets/hand.png" class="upload-view-img m10" />
				</el-upload>
			</div>
		</div>

		<div class="input_view m10">
			<div style="display: flex;justify-content: space-between">
				<div class="upload-view-text">解封申请书</div>
				<div class="upload-view-default" @click="seeDefault">查看示例</div>
			</div>
			<div class="upload-view">
				<el-upload class="upload-view m10" :action="action" :limit="1" :file-list="fileList" ref="upload3"
					list-type="picture-card" :on-success="handleSuccess3">
					<img src="./assets/shenqing.png" class="upload-view-img m10" />
				</el-upload>
			</div>
		</div>
		<el-button style="width: 100%;margin-top: 50px;margin-bottom: 100px;" @click="tapSubmit" type="primary"
			v-loading.fullscreen.lock="loading">确定</el-button>

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
				action: "https://api.mypallet.org/api/upload_oss",
				userIDText: "",
				nameText: "",
				idNumersText: "",
				fileList: [],
				file1: "",
				file2: "",
				file3: "",
				loading: false,
				dialogVisible: false,
			};
		},
		methods: {

			handleSuccess1(response, file, fileList) {
				var res_code = response.res_code;
				if (res_code == 0) {
					var data = response.data;
					this.file1 = data;
				} else {
					var res_msg = response.res_msg;
					this.showMessage(res_msg, "warning");
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

			tapSubmit() {

				var user_id = this.userIDText;
				var username = this.nameText;
				var self_num = this.idNumersText;
				var front_of_id_card = this.file1;
				var hand_of_id_card = this.file2;
				var proposer = this.file3;

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

				this.loading = true;

				var url = "https://api.mypallet.org/api/userProposer/create";
				var params = {
					"user_id": user_id,
					"username": username,
					"self_num": self_num,
					"front_of_id_card": front_of_id_card,
					"hand_of_id_card": hand_of_id_card,
					"proposer": proposer
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
		width: 80px;
	}

	.custom-input-bg .el-input__inner {

		color: #000000;
		border: 0;
		font-size: 15px;
		background-color: transparent;

	}

	.upload-view {

		display: flex;
		justify-content: space-between;
		align-items: center;
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