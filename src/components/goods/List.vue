<template>
	<div>
		<el-breadcrumb separator="/">
			<el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
			<el-breadcrumb-item>商品管理</el-breadcrumb-item>
			<el-breadcrumb-item>商品列表</el-breadcrumb-item>
		</el-breadcrumb>

		<!-- 卡片 -->
		<el-card>
			<el-row :gutter="20">
				<el-col :span="8">
					<el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getGoodsList">
						<el-button slot="append" icon="el-icon-search" @click="getGoodsList"></el-button>
					</el-input>
				</el-col>
				<el-col :span="4">
					<el-button type="primary" @click="goAddPage">添加商品</el-button>
				</el-col>
			</el-row>


			<!-- 表格 -->
			<el-table :data="goodsList" border stripe class="elTableTop">
				<el-table-column type="index"></el-table-column>
				<el-table-column label="商品名称" prop="goods_name"></el-table-column>
				<el-table-column label="商品价格(元)" prop="goods_price"></el-table-column>
				<el-table-column label="商品重量" prop="goods_weight"></el-table-column>
				<el-table-column label="创建时间" prop="add_time">
					<template slot-scope="scope">
						{{scope.row.add_time | dateFormat}}
					</template>
				</el-table-column>
				<el-table-column label="操作">
					<template slot-scope="scope">
						<el-button type="primary" icon="el-icon-edit" size="mini"></el-button>
						<el-button type="danger" icon="el-icon-delete" size="mini" @click="removeById(scope.row.goods_id)"></el-button>
					</template>
				</el-table-column>
			</el-table>

			<!-- 分页 -->
			<el-pagination class="mtPagin" @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="queryInfo.pagenum"
			 :page-sizes="[2, 5, 10, 15]" :page-size="queryInfo.pagesize" layout="total, sizes, prev, pager, next, jumper"
			 :total="total" background>
			</el-pagination>
		</el-card>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				queryInfo: {
					query: '',
					pagenum: 1,
					pagesize: 10
				},
				total: 0,
				goodsList: []
			}
		},
		created() {
			this.getGoodsList();
		},
		methods: {
			//根据分页获取数据
			async getGoodsList() {
				let {
					data: res
				} = await this.$axios.get('goods', {
					params: this.queryInfo

				})

				if (res.meta.status !== 200) {
					return this.$message.error('获取商品列表失败');
				}

				this.$message.success('获取商品列表成功');
				this.total = res.data.total;
				this.goodsList = res.data.goods;
			},
			handleSizeChange(newsize) {
				this.queryInfo.pagesize = newsize;

				this.getGoodsList();
			},
			handleCurrentChange(newpage) {
				this.queryInfo.pagenum = newpage;
				this.getGoodsList()
			},
			async removeById(id) {
				let confirmResult = await this.$confirm('此操作将永久删除该参数, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).catch(err => err);
				
				if(confirmResult !== 'confirm'){
					return this.$message.info('已经取消删除');
				}
				
				let {data:res} = await this.$axios.delete(`goods/${id}`);
				if(res.meta.status !== 200){
					return this.$message.error('删除失败');
				}
				
				this.$message.success('删除成功');
				this.getGoodsList();
			},
			
			// 添加商品
			goAddPage(){
				this.$router.push('/goods/add');
			}
		}
	}
</script>

<style scoped>
	.mtPagin {
		margin-top: 15px;
	}

	.elTableTop {
		margin-top: 15px;
	}
</style>
