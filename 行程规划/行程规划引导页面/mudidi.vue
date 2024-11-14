<template>
	<view class="container">
		<view class="header">AI智能规划行程</view>
		<!-- 基础信息区域 -->
		<view class="basic-info">
			<!-- 出发地 -->
			<view class="row">
				<text class="label">出发地</text>
				<uni-data-picker placeholder="请选择地址" popup-title="请选择所在地区" :preload="true" :step-searh="true"
					self-field="code" parent-field="parent_code" collection="opendb-city-china" orderby="code asc"
					field="name as text, code as value, eq(type, 2) as isleaf"  @popuphide="handlePickerChange">
				</uni-data-picker>
			</view>

			<!-- 目的地 -->
			<view class="row">
				<text class="label">目的地</text>
				<uni-data-picker placeholder="请选择地址" popup-title="请选择所在地区" :preload="true" :step-searh="true"
					self-field="code" parent-field="parent_code" collection="opendb-city-china" orderby="value asc"
					field="code as value, name as text, eq(type, 2) as isleaf" v-model="destination">
				</uni-data-picker>
			</view>

			<!-- 出行日期 -->
			<view class="row">
				<text class="label">出行日期</text>
				<input type="date" v-model="travelDate" class="input" />
			</view>

			<!-- 计划出游时长 -->
			<view class="row">
				<text class="label">出游时长</text>
				<view class="counter">
					<button @click="adjustDays(-1)" :disabled="travelDays <= 1" >-</button>
					<text>{{ travelDays }}天</text>
					<button @click="adjustDays(1)" >+</button>
				</view>
			</view>
		</view>

		<!-- 其他选项区域 -->
		<view class="additional-info">
			<!-- 出行人数 -->
			<view class="row">
				<text class="section-title">出行人数</text>
				<view class="counter">
					<button @click="adjustPeople(-1)" :disabled="peopleCount <= 1">-</button>
					<text>{{ peopleCount }}</text>
					<button @click="adjustPeople(1)">+</button>
				</view>
			</view>
			<view class="row">
				<view class="options">
					<button
						v-for="(people, index) in people"
						:key="index"
						@click="selectPeople(index)"
						:class="{ active: selectedPeople.includes(index) }"
					>
						{{ people }}
					</button>
				</view>
			</view>

			<!-- 酒店级别 -->
			<view class="row">
				<text class="section-title">酒店级别</text>
			</view>	
			<view class="row">
				<view class="options">
					<button
						v-for="(level, index) in hotelLevels"
						:key="index"
						@click="selectHotel(index)"
						:class="{ active: selectedHotels.includes(index) }"
					>
						{{ level }}
					</button>
				</view>
			</view>	
			<!-- 出行方式 -->
			<view class="row">
				<text class="section-title">出行方式</text>
			</view>	
			<view class="row">
				<view class="options">
					<button
						v-for="(travel, index) in travels"
						:key="index"
						@click="selectTravel(index)"
						:class="{ active: selectedTravels.includes(index) }"
					>
						{{ travel }}
					</button>
				</view>
			</view>	
			<!-- 提交按钮 -->
			
		</view>
		<view class="additional-info">
			<!-- 目的地偏好 -->
			<view class="row">
				<text class="section-title">目的地偏好</text>
			</view>	
			<view class="row">
				<view class="options">
					<button
						v-for="(destination, index) in destinations"
						:key="index"
						@click="selectDestination(index)"
						:class="{ active: selectedDestinations.includes(index) }"
					>
						{{ destination }}
					</button>
				</view>
			</view>	
			<view class="row">
				<text class="section-title">活动偏好</text>
			</view>	
			<view class="row">
				<view class="options">
					<button
						v-for="(activity, index) in activities"
						:key="index"
						@click="selectActivity(index)"
						:class="{ active: selectedActivities.includes(index) }"
					>
						{{ activity }}
					</button>
				</view>
			</view>
			<view class="row">
				<text class="section-title">饮食偏好</text>
			</view>	
			<view class="row">
				<view class="options">
					<button
						v-for="(food, index) in foods"
						:key="index"
						@click="selectFood(index)"
						:class="{ active: selectedFoods.includes(index) }"
					>
						{{ food }}
					</button>
				</view>
			</view>
		</view>
		<view class="additional-info">
			<view class="row">
				<text class="section-title">旅游模式</text>
			</view>	
			<view class="row">
				<view class="options">
					<button
						v-for="(mode, index) in mode"
						:key="index"
						@click="selectMode('mode', index)"
						:class="{ active: selectedMode === index }"
					>
						{{ mode }}
					</button>
				</view>
			</view>
			<view class="row">
					<!-- 标题 -->
					<view class="section-title">预算规划</view>
			</view>
			<view class="row">
					<!-- 各项预算范围 -->
					<view class="budget-rows">
						<view class="budget-item" v-for="(item, index) in budgets" :key="index">
							<text class="budget-label">{{ item.label }}</text>
							<input
								class="budget-input"
								type="number"
								:min="item.min"
								:max="item.max"
								v-model.number="item.range[0]"
								placeholder="最小值"
							/>
							<span>~</span>
							<input
								class="budget-input"
								type="number"
								:min="item.min"
								:max="item.max"
								v-model.number="item.range[1]"
								placeholder="最大值"
							/>
							<text class="budget-unit">元</text>
						</view>
					</view>
					</view>
					<view class="row">
					    <text class="section-title">其他</text>
					</view>
					<view class="row">
					    <textarea 
					        v-model="otherNeeds" 
					        class="other-input" 
					        placeholder="请输入你的额外需求，比如特殊饮食、残疾通道需求等"
					        maxlength="200"
					        show-count
					    ></textarea>
					</view>
					
		</view>
		<view class="submit-button">
				<button @click="submitPlan">立即生成我的自定义行程</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			departure: "",
			destination: "",
			travelDate: "",
			travelDays: 3,
			peopleCount: 3,
			people:["老人同行","儿童同行","孕妇"],
			hotelLevels: ["民宿", "经济", "舒适", "豪华"],
			travels:["自驾","飞机","火车","高铁","公共交通","打车","步行"],
			destinations:["城市探险","海滩度假","自然风光","冒险旅行","文化体验","亲子旅行","蜜月度假","毕业旅行"],
			activities:["文化探索","户外冒险","水上活动","美食之旅","节庆活动","休闲放松"],
			foods:["地道美食","米其林","有机食品","特色餐厅","海鲜大餐","素食主义"],
			mode:["特种兵旅游","休闲游"],
			selectedHotels: [], // 用于存储选中的酒店索引
			selectedTravels:[],
			selectedDestinations:[],
			selectedActivities:[],
			selectedFoods:[],
			selectedPeople:[],
			selectedMode:null,
			// 各类预算数据
						budgets: [
							{ label: "总预算", range: [0, 10000], min: 0, max: 10000 },
							{ label: "住宿预算", range: [0, 5000], min: 0, max: 5000 },
							{ label: "餐饮预算", range: [0, 2000], min: 0, max: 2000 },
							{ label: "交通预算", range: [0, 2000], min: 0, max: 2000 },
							{ label: "娱乐预算", range: [0, 1000], min: 0, max: 1000 },
						],
						// 偏重取舍
						weights: [
							{ label: "经济", value: 5 },
							{ label: "舒适", value: 5 },
						],	
			otherNeeds: "", // 用户的额外需求
		};
	},
	methods: {
		adjustDays(value) {
			this.travelDays = Math.max(1, this.travelDays + value);
		},
		adjustPeople(value) {
			this.peopleCount = Math.max(1, this.peopleCount + value);
		},
		selectPeople(index){
			const selectedIndex = this.selectedPeople.indexOf(index);
			    if (selectedIndex > -1) {
			      // 如果已选中，移除
			      this.selectedPeople.splice(selectedIndex, 1);
			    } else {
			      // 如果未选中，添加
			      this.selectedPeople.push(index);
			    }
		},
		selectHotel(index) {
			
			const selectedIndex = this.selectedHotels.indexOf(index);
			    if (selectedIndex > -1) {
			      // 如果已选中，移除
			      this.selectedHotels.splice(selectedIndex, 1);
			    } else {
			      // 如果未选中，添加
			      this.selectedHotels.push(index);
			    }
			  },
		selectTravel(index) {
			
			const selectedIndex = this.selectedTravels.indexOf(index);
			    if (selectedIndex > -1) {
			      // 如果已选中，移除
			      this.selectedTravels.splice(selectedIndex, 1);
			    } else {
			      // 如果未选中，添加
			      this.selectedTravels.push(index);
			    }
			  },
		selectDestination(index) {
			
			const selectedIndex = this.selectedDestinations.indexOf(index);
			    if (selectedIndex > -1) {
			      // 如果已选中，移除
			      this.selectedDestinations.splice(selectedIndex, 1);
			    } else {
			      // 如果未选中，添加
			      this.selectedDestinations.push(index);
			    }
			  },
		selectActivity(index) {
			
			const selectedIndex = this.selectedActivities.indexOf(index);
			    if (selectedIndex > -1) {
			      // 如果已选中，移除
			      this.selectedActivities.splice(selectedIndex, 1);
			    } else {
			      // 如果未选中，添加
			      this.selectedActivities.push(index);
			    }
			  },
		selectFood(index) {
			
			const selectedIndex = this.selectedFoods.indexOf(index);
			    if (selectedIndex > -1) {
			      // 如果已选中，移除
			      this.selectedFoods.splice(selectedIndex, 1);
			    } else {
			      // 如果未选中，添加
			      this.selectedFoods.push(index);
			    }
			  },
		selectMode(type,index){
			if(type==="mode") this.selectedMode=index;
		},
		handlePickerChange(event) {
			const node = event.detail.node || {};
			this.departure = node.text; // 取城市名字
		},
		submitPlan() {
			const selectedPeople = this.selectedPeople.map(index => this.people[index]);
			const selectedHotelLevels = this.selectedHotels.map(index => this.hotelLevels[index]);
			const selectedTravels = this.selectedTravels.map(index => this.travels[index]);
			const selectedDestinations = this.selectedDestinations.map(index => this.destinations[index]);
			const selectedActivities = this.selectedActivities.map(index => this.activities[index]);
			const selectedFoods = this.selectedFoods.map(index => this.foods[index]);
			// 收集预算范围数据
			    const budgetData = this.budgets.map(budget => {
			        return {
			            label: budget.label,
			            min: budget.range[0],
			            max: budget.range[1],
			        };
			    })
			console.log("提交行程：", {
				departure: this.departure,
				destination: this.destination,
				travelDate: this.travelDate,
				travelDays: this.travelDays,
				peopleCount: this.peopleCount,
				selectedPeople:selectedPeople,
				selectedHotel: selectedHotelLevels,
				selectedTravel: selectedTravels,
				selectedFood: selectedFoods,
				selectedMode:this.mode[this.selectedMode],
				budgets: budgetData,
				otherNeeds: this.otherNeeds, // 新增的“其他”需求
			});
		},
},
};
</script>

<style>
	
.container {
	padding: 20px;
	font-family: Arial, sans-serif;
}
.header {
	font-size: 24px;
	font-weight: bold;
	text-align: center;
	margin-bottom: 20px;
}
.basic-info,
.additional-info {
	background-color: white;
	padding: 15px;
	border-radius: 8px;
	margin-bottom: 20px;
}
.row {
	display: flex;
	align-items: center;
	justify-content: space-between;
	margin-bottom: 15px;
}
.label {
	width: 80px;
	font-size: 16px;
	margin-right: 10px;
}
.input {
	flex: 1;
	padding: 6px;
	font-size: 14px;
	border: 1px solid #ccc;
	border-radius: 4px;
}
.counter {
	display: flex;
	align-items: center;
	justify-content: space-between;
}
.counter button {
	width: 30px;
	height: 30px;
	font-size: 12px;
	padding: 0;
	text-align: center;
	border: 1px solid #ccc;
	background-color: #fff;
	border-radius: 4px;
	
}
.options {
	display: flex;
	flex-wrap: wrap;
	gap: 10px;
}
.options button {
	padding: 10px 19px;
	background-color: #f0f0f0;
	border: none;
	border-radius: 5px;
	cursor: pointer;
	font-size: 14px;
}
.options button.active {
	background-color: #91D5FF;
	color: white;
}
.submit-button button {
	width: 100%;
	padding: 10px;
	font-size: 16px;
	background-color: #91D5FF;
	color: white;
	border: none;
	border-radius: 5px;
	cursor: pointer;
}
.budget-section {
	padding: 20px;
	background-color: #f9f9f9;
	border-radius: 10px;
	box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
	margin: 20px;
}

.section-title {
	font-size: 18px;
	font-weight: bold;
	margin-bottom: 15px;
	color: #333;
}

.budget-rows {
	display: flex;
	flex-direction: column;
	gap: 10px;
}

.budget-item {
	display: flex;
	align-items: center;
	gap: 10px;
}

.budget-label {
	width: 80px;
	font-size: 16px;
	color: #555;
}

.budget-input {
	width: 60px;
	padding: 5px;
	border: 1px solid #ccc;
	border-radius: 5px;
	text-align: center;
}

.budget-unit {
	color: #999;
	font-size: 14px;
}

.other-section {
    margin-top: 20px;
    padding: 10px;
    background-color: #f9f9f9;
    border-radius: 8px;
}

.section-title {
    font-size: 16px;
    font-weight: bold;
    margin-bottom: 10px;
    color: #333;
}

.other-input {
    width: 100%;
    height: 80px;
    padding: 10px;
    font-size: 14px;
    border: 1px solid #ccc;
    border-radius: 8px;
    background-color: #fff;
    resize: none;
}
</style>
