<template>
	<div>
		<fixedParts></fixedParts>
		<div class="interviewTable">
			<h3 style="margin-top: 15px;margin-bottom: 20px;">历史面试</h3>
			<el-button @click="resetDateFilter">清除日期过滤器</el-button>
			<el-button @click="clearFilter">清除所有过滤器</el-button>
			<el-table ref="filterTable" :data="interviewList" style="width: 100%">
				<el-table-column prop="company" label="公司" width="180" :formatter="formatter">
				</el-table-column>
				<el-table-column prop="title" label="名称" width="100" :formatter="formatter">
				</el-table-column>
				<!-- <el-table-column prop="interviewer" label="面试官" :formatter="formatter">
				</el-table-column> -->
				<el-table-column prop="problemCount" label="题目数量" :formatter="formatter">
				</el-table-column>
				<el-table-column prop="finalResult" label="结果" :formatter="formatter">
				</el-table-column>
				<el-table-column prop="chatCount" label="留言数量" :formatter="formatter">
				</el-table-column>
			</el-table>
		</div>
	</div>
</template>

<script>
	import fixedParts from '../components/fixedParts.vue'
	export default {
		components: {
			fixedParts
		},
		data() {
			return {
				tempList: [],
				interviewList: []

			}
		},
		methods: {
			getHistoryInterview() {
				return new Promise(resolve => {
					setTimeout(() => {
						let that = this;
						this.$axios.get('/api/historyInterview', {
								params: {
									email: this.COMMON.user
								}
							})
							.then((res) => {
								if (res.data.status == '0') {
									console.log('1', res);
									that.tempList = res.data.result.list;
									//console.log(that.tempList);
								} else {
									console.log('getInfo status Error!');
								}

							})
							.catch((error) => {
								console.log(error);
							});
					}, 3000)
				});
			},
			loadInterviewList() {
				// return new Promise(resolve => {
				//    setTimeout(() => {
				console.log('2');
				console.log(this.tempList);
				let i = 0;
				for (i = 0; i < this.tempList.length; i++) {
					console.log(tempList[i]['company']);
					let temp = {
						company: tempList[i]['company'],
						title: tempList[i]['title'],

						problemCount: tempList[i]['problem'].length,
						finalResult: tempList[i]['finalResult'],
						chatCount: tempList[i]['chat'].length
					}
					this.interviewList.append(temp);
				}
				// }, 1000)
				//  });
			},
			async init() {
				await this.getHistoryInterview();
				console.log(this.tempList);
				this.loadInterviewList();
			},
			resetDateFilter() {
				this.$refs.filterTable.clearFilter('date');
			},
			clearFilter() {
				this.$refs.filterTable.clearFilter();
			},
			formatter(row) {

				return row.address;
			},
			filterTag(value, row) {
				return row.tag === value;
			},
			filterHandler(value, row, column) {
				const property = column['property'];
				return row[property] === value;
			}
		},
		created() {

		},
		mounted() {

			this.init();
		}
	}
</script>
<style scoped>
	.interviewTable {
		margin-top: 15px;
		margin-left: 26%;
		margin-right: 20px;
	}
</style>
