<template>
	<div id="app">
		<el-row :gutter="12" >
			<el-col :span="12" style="margin-bottom:40px">
				<el-card shadow="hover" style="background: #eff7ff;">
					<div slot="header" class="clearfix">
						CHECK选项
					</div>
					<div class="text item" style="height: 50em;overflow: auto;">
						<el-input v-model="image_id" placeholder="请输入image_id" style="margin: 5px;"></el-input>

						<el-dropdown @command="handleCommand">
							<el-button type="primary">
								增加CHECK<i class="el-icon-arrow-down el-icon--right"></i>
							</el-button>
							<el-dropdown-menu slot="dropdown">
								<el-dropdown-item command="login" :disabled="formLoginVisible">登录</el-dropdown-item>
								<el-dropdown-item command="check">CHECK</el-dropdown-item>
								<!-- <el-dropdown-item command="e" divided>蚵仔煎</el-dropdown-item> -->
							</el-dropdown-menu>
						</el-dropdown>
						<el-card shadow="hover" style="margin: 10px;" v-show="formLoginVisible">
							<div slot="header" class="clearfix">
								LOGIN
								<el-button type="danger" style="float: right;" @click="cancelLogin()">取消</el-button>
							</div>
							<div class="text item">
								<el-form ref="form" :model="form.login" label-width="100px">
									<el-form-item label="登录URL">
										<el-input v-model="form.login.url"></el-input>
									</el-form-item>
								</el-form>
								<el-form ref="form" :model="form.login" label-width="100px">
									<el-form-item label="用户名字段">
										<el-input v-model="form.login.username.filed"></el-input>
									</el-form-item>
								</el-form>
								<el-form ref="form" :model="form" label-width="100px">
									<el-form-item label="用户名">
										<el-input v-model="form.login.username.value"></el-input>
									</el-form-item>
								</el-form>
								<el-form ref="form" :model="form" label-width="100px">
									<el-form-item label="密码字段">
										<el-input v-model="form.login.password.filed"></el-input>
									</el-form-item>
								</el-form>
								<el-form ref="form" :model="form" label-width="100px">
									<el-form-item label="密码">
										<el-input v-model="form.login.password.value"></el-input>
									</el-form-item>
								</el-form>
							</div>
						</el-card>


						<el-card shadow="hover" style="margin: 10px;" :key="i" v-for="(check,i) in form.check">
							<div slot="header" class="clearfix">
								{{check.name}}
								<el-button type="danger" style="float: right;" @click="cancelCheck(i)">取消</el-button>

							</div>
							<el-form ref="form" :model="check" label-width="100px">
								<el-form-item>
									<el-button type="success" style="float: right;" @click="addReq(i)">增加请求</el-button>
								</el-form-item>
								<div class="text item" v-for="(req,j) in check.req" :key="j">
									<el-divider>请求</el-divider>
									<el-form-item>
										<el-button type="danger" style="float: right;" @click="deleteReq(i,j)">删除请求</el-button>
									</el-form-item>
									<el-form-item label="checkURL">
										<el-input v-model="req.url"></el-input>
									</el-form-item>
									<el-form-item label="请求方法" label-width="100px">
										<el-select v-model="req.method" placeholder="请选择请求方法" style="width: 100%;">
											<el-option label="GET" value="get"></el-option>
											<el-option label="POST" value="post"></el-option>
										</el-select>
									</el-form-item>
									<el-form-item>
										<el-button type="success" style="float: right;" @click="addParameter(i,j)">增加参数</el-button>
									</el-form-item>
									<div v-for="(parameter,k) in req.parameter">
										<el-divider content-position="left">参数</el-divider>
										<el-form-item label="KEY">
											<el-input v-model="parameter.key"></el-input>
										</el-form-item>
										<el-form-item label="VALUE">
											<el-input v-model="parameter.value"></el-input>
										</el-form-item>
										<el-form-item>
											<el-button type="danger" style="float: right;" @click="deleteParameter(i,j,k)">删除参数</el-button>
										</el-form-item>
										<el-divider></el-divider>
									</div>
									<el-form-item label="返回字符串">
										<el-input v-model="req.resp"></el-input>
									</el-form-item>
								</div>

								<el-divider></el-divider>
							</el-form>
						</el-card>
					</div>
				</el-card>
			</el-col>
			<el-col :span="12" style="margin-bottom:40px">
				<el-card shadow="hover" style="background: #eff7ff;">
					<div slot="header" class="clearfix">
						CHECK代码
						<el-button type="success" style="float: right; margin: 5px;" @click="updateCode()">更新代码</el-button>
						<el-button type="warning" style="float: right; margin: 5px;" @click="">复制代码</el-button>
					</div>

					<div class="text item" style="height: 50em;overflow: auto;">
						<div style="margin: 20px 0;"></div>
						
						<el-input
						  type="textarea"
						  rows="40"
						  style="font-size: small;"
						  placeholder="请输入内容"
						  v-model="code">
						</el-input>
					</div>
				</el-card>
			</el-col>
		</el-row>
	<code style="display: none;" id="code">
	from bs4 import BeautifulSoup
	
	from checker.checker import CheckerTemplate, ProblemTemplate
	
	
	class dvwaCheck(CheckerTemplate):
	    checker_problem_template = ProblemTemplate.objects.get(image_id='{{image_id}}')
	</code>
	<code style="display: none;" id="prepare_post">
	    def _prepare(self):
	        url = self.url + '{{form.login.url}}'
	        data = {'{{form.login.username.filed}}': '{{form.login.username.value}}', '{{form.login.password.filed}}': '{{form.login.password.value}}'}
	        res = self.session.post(url, data=data)
	        return True
	</code>
	<code style="display: none;" id="prepare_get">
	    def _prepare(self):
	        url = self.url + '{{form.login.url}}'
	        data = {'{{form.login.username.filed}}': '{{form.login.username.value}}', '{{form.login.password.filed}}': '{{form.login.password.value}}'}
	        res = self.session.get(url, params=data)
	        return True
	</code>
	<code style="display: none;" id="check">
	    def {check_name}(self):
            {req} 
		return True, 'ok'
	</code>
	<code style="display: none;" id="req_get">
		url = self.url + '{url}'
		data ={data}
		res = self.session.get(url,params=data)
		if '{resp}' not in res.text:
			return False, 'down'
	</code>
	<code style="display: none;" id="req_post">
		url = self.url + '{url}'
		data = {data}
		res = self.session.post(url,data=data)
		if '{resp}' not in res.text:
			return False, 'down'
	</code>
		<!-- <img src="./assets/logo.png">
    <div>
      <el-button @click="startHacking">Start</el-button>
    </div> -->
	</div>
</template>

<script>
	export default {
		data() {
			return {
				code: "",
				image_id: "",
				formLoginVisible: false,
				form: {
					login: {
						url: "",
						method: "post",
						username: {
							filed: "",
							value: "",
						},
						password: {
							filed: "",
							value: "",
						},
					},
					check: []

				}
			}
		},
		methods: {
			updateCode(){
				
					var codeTemple = document.getElementById("code").innerHTML;
					// this.code = code.innerHTML;
					this.code = codeTemple;
					if(this.form.login.method=="post"){
						var prepareTemple = document.getElementById("prepare_post").innerHTML;
					}else{
						var prepareTemple = document.getElementById("prepare_get").innerHTML;
					}
					this.code = this.code + prepareTemple;
					var all_check = "";
					for (var i=0; i<this.form.check.length;i++){
						var check_req = "";
						for(var j=0; j<this.form.check[i].req.length;j++){
							console.log(i,j);
							var req = this.getReqCode(i,j);
							check_req = check_req + req;
						}
						var check_text = this.getCheckCode(i,check_req);
						all_check = all_check + check_text;
						console.log(check_text);
					}
					this.code = this.code + all_check;
					
					// this.getReqCode(0,0);
					// console.log(this.code);
			},
			getCheckCode(i,req){
				var check = this.form.check[i];
				var checkTemple = document.getElementById("check").innerHTML;
				var checkText = checkTemple.replace('{check_name}',check.name);
				checkText= checkText.replace('{req}',req);
				return  checkText
			},
			getReqCode(i,j){
				var req = this.form.check[i].req[j];
				// console.log(req);
				if(req.method=="get"){
					var reqTemple = document.getElementById("req_get").innerHTML;
				}else{
					var reqTemple = document.getElementById("req_post").innerHTML;
				}
				var req_text = "";
				req_text = reqTemple.replace("{url}",req.url)
				req_text = req_text.replace("{resp}",req.resp)
				// 'username': 'admin', 'password': 'password',
				var data = "";
				// var k;
				for (i=0 ; i<req.parameter.length;i++){
					data = data+"'" +req.parameter[i]["key"] +"': '"  + req.parameter[i]["value"]+ "',"
				}
				data = "{"+data+"}"
				req_text = req_text.replace("{data}",data)
				return req_text;
			},
			cancelLogin() {
				this.formLoginVisible = false;
			},
			cancelCheck(i) {
				this.form.check.splice(i, 1);
			},
			addCheck() {
				var len = this.form.check.length + 1;
				this.form.check.push({
					name: "checker" + len,
					req: [{
						url: "/",
						method: "post",
						parameter: [{
							key: "",
							value: ""
						}],
						resp: "",
					}]

				});
			},
			deleteParameter(i, j, k) {
				this.form.check[i].req[j].parameter.splice(k, 1);
				console.log(this.form.check[i].req[j].parameter[k]);
			},
			deleteReq(i, j) {
				this.form.check[i].req.splice(j, 1);
			},
			addParameter(i, j) {
				console.log(this.form.check[i].req[j]);
				this.form.check[i].req[j].parameter.push({
					key: "",
					value: ""
				})
				// this.form.check.
			},
			addReq(i) {
				this.form.check[i].req.push({

					url: "",
					method: "GET",
					parameter: [{
						key: "",
						value: ""
					}],
					resp: "",

				});
				console.log(this.form.check[i]);
			},
			handleCommand(command) {
				if (command == "login") {
					this.formLoginVisible = true;
				}
				if (command == "check") {
					this.addCheck();
				}
				this.$message('click on item ' + command);
			},
			startHacking() {
				this.$notify({
					title: 'It works!',
					type: 'success',
					message: 'We\'ve laid the ground work for you. It\'s time for you to build something epic!',
					duration: 5000
				})
			}
		}
	}
</script>

<style>
	#app {
		font-family: Helvetica, sans-serif;
		text-align: center;
	}
</style>
