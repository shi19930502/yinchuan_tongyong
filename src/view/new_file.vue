<template>
	<div class="index">
		<div :style='bannerStyle' class="header">
			<van-notice-bar
				v-if='isNoticeShow'
				@click='noticeClick'
				:text="newsEnityList[0].title"
			  	:left-icon="gb_logo"
			/>
			<router-link :to="{name:'enterprise',query:{traceablityNo:$route.query.traceablityNo}}">
				<div :style='qiyeStyle' class="header_left">企业信息</div>
			</router-link>
			<router-link v-if='farmEntityView' :to="{name:'base',query:{traceablityNo:$route.query.traceablityNo}}">
				<div class="header_right">基地信息</div>
			</router-link>
		</div>
		<div class="content" v-if='isContentShow'>
			<div class="product_info">
				<div class="content_header">产品信息</div>
				<div class="p_i_content">
					<img :style='imgStyle' :src="varietyEntityView.photo" alt="" />
					<ul>
						<li>{{varietyEntityView.breedName}}</li>
						<li>{{company.name}}</li>
						<li>品种：<span>{{varietyEntityView.breedName}}</span></li>
						<li>规格：<span>{{varietyEntityView.specifications}}</span></li>
						<li>等级：<span>{{varietyEntityView.gradeName}}</span></li>
						<li v-if='varietyEntityView.organicCropsName=="是"'>是否有机：<span>{{varietyEntityView.organicCropsName}}</span></li>
						<li>生产时间：<span>{{varietyEntityView.production}}</span></li>
						<li>过期时间：<span>{{varietyEntityView.expirationDate}}</span></li>
					</ul>
				</div>
				<div v-if='productInforShow' class="h_i_content">
					<div v-if='productInfor.photo!=null' class="h_i_content_img">
						<img :src="productInfor.photo" alt="" />
					</div>
					<div class="h_i_c_bottom">
						<div class="h_i_c_bottom_center">
							<div>检测结果：<span class="hege">{{productInfor.result}}</span></div>
							<div>检测标准：<span>{{productInfor.inspectionStandardName}}</span></div>
							<div>检测人：<span>{{productInfor.qualityStaff}}</span></div>
							<div>检测类型：<span>{{productInfor.reportTypeName}}</span></div>
						</div>
					</div>
				</div>
			</div>
			<!--生产过程；种植过程；养殖过程-->
			<div v-if='cultureEntityViewShow||processEntityListShow' class="production_process">
				<div class="content_header">{{record}}</div>
				<div v-if='cultureEntityViewShow' class="record_time">
					<div class="record_time_c">
						<div class="record_time_c_n">开始时间</div>
						<div class="record_time_c_c">{{cultureEntityView.startDate}}</div>
					</div>
					<div class="record_hx"></div>
					<div class="record_start_c">
						<div class="record_time_c_n">结束时间</div>
						<div class="record_time_c_c">{{cultureEntityView.endEnd}}</div>
					</div>
				</div>
				<div v-if='processEntityListShow' class="farm">
					<div class="swiper-container farmSwiper" id='farm'>
						<div class="swiper-wrapper">

						</div>
						<!-- 如果需要分页器 -->
						<div class="swiper-pagination-farm"></div>
					</div>
				</div>
			</div>
			<!--收获信息-->
			<div v-if='recoveryEntityViewShow' class="harvest_info">
				<div class="content_header">{{messageName}}</div>
				<ul>
					<li>{{recoveryEntityView.breedName}}</li>
					<li>规格：<span>{{recoveryEntityView.specifications}}</span></li>
					<li v-if='isJinheImgShow'><img src="../assets/img/sh_jinhe.png" alt="" /></li>
					<li>等级：<span>{{recoveryEntityView.gradeName}}</span></li>
					<li>收货人：<span>{{recoveryEntityView.operatorStaffName}}</span></li>
					<li>收获时间：<span>{{recoveryEntityView.harvestDate}}</span></li>
				</ul>
				<div class="h_i_content">
					<div v-if='recoveryEntityView.photo!=null ' class="h_i_content_img">
						<img :src="recoveryEntityView.photo" alt="" />
					</div>
					<div class="h_i_c_bottom">
						<div class="h_i_c_bottom_center">
							<div>检测结果：<span class="hege">{{recoveryEntityView.result}}</span></div>
							<div>检测标准：<span>{{recoveryEntityView.inspectionStandardName}}</span></div>
							<div>检测人：<span>{{recoveryEntityView.qualityStaff}}</span></div>
							<div>检测类型：<span>{{recoveryEntityView.reportTypeName}}</span></div>
						</div>
					</div>
				</div>
			</div>
			<!--加工信息-->
			<div v-if='productInforShow' class="harvest_info process_info">
				<div class="content_header">加工信息</div>
				<ul>
					<li>{{productInfor.breedName}}</li>
					<li>规格：<span>{{productInfor.specifications}}</span></li>
					<li>等级：<span>{{productInfor.gradeName}}</span></li>
					<li>负责人：<span>{{productInfor.operatorStaffName}}</span></li>
					<li>生产时间：<span>{{productInfor.operationTime}}</span></li>
				</ul>
				<!--加工信息的生产过程-->
				<div class="process" v-if='productInforListProcessShow'>
					<div class="swiper-container" id='process'>
						<div class="swiper-wrapper">
							<div class="swiper-slide" v-for="(item, index) in productInforListProcess">
								<div class="content_header">加工过程</div>
								<div class='biaoqian'>{{index+1}}</div>
								<ul>
									<li>{{item.name}}</li>
									<li>{{item.operationDateStr}}</li>
									<li class="thr" v-if="item.photo||item.video">
										<div class="swiper-container videoSwiper">
											<div class="swiper-wrapper">
												<div v-if='item.video' class="swiper-slide">
													<!--autoplay="autoplay" controls="controls"-->
													<!--poster="../assets/img/banner.png"-->
													<video id="video1" poster="../assets/img/video.png" controls="controls">
														<source :src="item.video" type="video/mp4" />
														<source id="ogg_src" :src="item.video" type="video/ogg"> 您的浏览器不支持 HTML5 video 标签。
													</video>
												</div>
												<div v-if='item.photo' v-for='itemPhoto in item.photo.split(",")' class="swiper-slide">
													<img :data-src="img_url+itemPhoto" alt="" class="swiper-lazy" />
												</div>
											</div>
											<!--<div class="swiper-pagination-video"></div>-->
										</div>
									</li>
									<li class="dian"><i></i>操作人：<span>{{productInfor.operatorStaffName}}</span></li>
									<li class="dian"><i></i>工作参数：<span>{{item.processParameters}}</span></li>
									<li class="dian"><i></i>作业环境：<span>{{item.operatingEnvironment}}</span></li>
									<li class="dian"><i></i>原料：<span v-for="(i,n) in item.outdetal">{{i.breedName}}<span v-if='n+1!=item.outdetal.length'>、</span></span>
									</li>
								</ul>
							</div>
						</div>
						<!-- 如果需要分页器 -->
						<div class="swiper-pagination-farm"></div>

					</div>
				</div>
			</div>
			<!--广玉的面食分享-->
			<div v-if='isCookedShow' class="cooked">
				<div class="content_header">{{cookbooksName}}</div>
				<div class="swiper-container cookedSwiper">
					<div class="swiper-wrapper">
						<div v-if='cookbooks[0].video!=null' class="swiper-slide">
							<!--autoplay="autoplay" controls="controls"-->
							<!--poster="../assets/img/banner.png"-->
							<video id="videoCooked" poster="../assets/img/video.png" controls="controls">
								<source :src="video_url+cookbooks[0].video" type="video/mp4" />
								<source id="ogg_src" :src="video_url+cookbooks[0].video" type="video/ogg"> 您的浏览器不支持 HTML5 video 标签。
							</video>
						</div>
						<div v-if='cookbooks[0].photo!=null' v-for='itemPhoto in cookbooks[0].photo.split(",")' class="swiper-slide">
							<img :data-src="img_url+itemPhoto" alt="" class="swiper-lazy" />
						</div>
					</div>
					<div class="swiper-pagination-cooked"></div>
				</div>
				<div v-if='cookbooks[0].describe!=null' class="text">
					{{cookbooks[0].describe}}
				</div>
			</div>
		</div>
		<!--面粉分享的弹出-->
		<!--v-if='isCookedShow'--> 
		<van-popup v-if='isPopupCookedShow' v-model="popupCookedShow">
			<div class="swiper-container popupCooked">
				<div class="swiper-wrapper">
					<div v-if='cookbooks[0].video!=null' class="swiper-slide">
						<!--autoplay="autoplay" controls="controls"-->
						<!--poster="../assets/img/banner.png"-->
						<video id="" poster="../assets/img/video.png" controls="controls">
							<source :src="video_url+cookbooks[0].video" type="video/mp4" />
							<source  :src="video_url+cookbooks[0].video" type="video/ogg"> 您的浏览器不支持 HTML5 video 标签。
						</video>
					</div>
					<div v-if='cookbooks[0].photo!=null' v-for='itemPhoto in cookbooks[0].photo.split(",")' class="swiper-slide">
						<img :data-src="img_url+itemPhoto" alt="" class="swiper-lazy" />
					</div>
				</div>
				<div class="swiper-pagination-cooked"></div>
			</div>
		</van-popup>
		<!--弹出-->
		<!--@click-overlay='removeSwiper'-->
		<van-popup v-model="popupShow">
			<div class="swiper-container videoShow" id='videoShow'>
				<div class="swiper-wrapper">

				</div>
				<!-- 如果需要分页器 -->
				<div class="swiper-pagination-videoShow"></div>
			</div>
		</van-popup>
	</div>
</template>

<script>
	import Vue from 'vue'
	import "./../scss/index.scss"
	import gb_logo from '../assets/img/icon_laba.png'
	import logo from '../assets/img/img.png'
	import videoImg from '../assets/img/video.png'
	import guangyu from '../assets/img/index_guangyu.png'
	import hefeng from '../assets/img/index_hefeng.png'
	import jinhe from '../assets/img/index_jinhe.png'
	import ningbao from '../assets/img/index_ningbao.png'
	import wanjia from '../assets/img/index_wanjia.png'
	import yijude from '../assets/img/index_yijude.png'
	import yufeng from '../assets/img/index_yufeng.png'
	import yuxin from '../assets/img/index_yuxin.png'
	import bannerOld from '../assets/img/img_img.png'
	import { Toast, Popup, Button, NoticeBar } from 'vant';
	var componentsArr = [Toast, Popup, Button, NoticeBar]
	Vue.use(function(Vue) {
		componentsArr.map(component => {
			Vue.component(component.name, component);
		});
		for(var key in componentsArr[0].components) {
			Vue.component(componentsArr[0].components[key].name, componentsArr[0].components[key]);
		}
	})
	export default {
		data() {
			return {
				popupShow: false,
				popupCookedShow:false,
				count: 0,
				farmEntityView: false,
				isContentShow: false,
				company: {
					name: ''
				},
				//产品信息
				varietyEntityView: {
					breedName: '',
					expirationDate: '',
					gradeName: '',
					organicCropsName: '',
					production: '',
					specifications: '',
					name: '',
					photo: '',
				},
				//农事过程
				processEntityList: [],
				processEntityListShow: false,
				//收获信息
				recoveryEntityView: {
					breedName: '',
					gradeName: '',
					harvestDate: '',
					inspectionStandardName: '',
					operatorStaffName: '',
					qualityStaff: '',
					photo: null,
					reportTypeName: '',
					result: '',
				},
				recoveryEntityViewShow: true,
				//加工信息
				productInfor: {
					breedName: '',
					gradeName: '',
					inspectionStandardName: '',
					operationTime: '',
					operatorStaffName: '',
					qualityStaff: '',
					reportTypeName: '',
					specifications: '',
					result: '',
					photo: null,
				},
				//加工信息的生产过程
				productInforListProcess: [],
				productInforListProcessShow: true,
				productInforShow: true,
				videoShowSwiperArr: [],
				//cli,要么图片放入static里面。要么url-loader不要7位数的hash，
				videoShowSwiperObj: {},
				appendArr: [],
				processName: '种植过程',
				messageName: '采收信息',
				record: '种植档案',
				img_url: IMG_URL,
				video_url: VIDEO_URL,
				cultureEntityView: {
					startDate: '',
					endEnd: '',
				},
				cultureEntityViewShow: false,
				bannerStyle: {},
				isJinheImgShow: false,
				isCookedShow: false,
				cookbooks: [],
				popupCookedSwiperObj:{},
				isPopupCookedShow:false,
				imgStyle:{},
				gb_logo:gb_logo,
				isNoticeShow:false,
				newsEnityList:[{
					title:'',
				}],
				qiyeStyle:{},
				cookbooksName:'',
			}
		},
		computed: {

		},
		mounted() {
			//title判断
			this.titleJudge();
			//弹出框的vedio Swiper
			this.videoShowSwiper();
			this.init();
		},
		methods: {
			init() {
				this.$root.ajax({
					url: API_URL + "traceablity/getTraceablityInfo",
					type: 'get',
					param: {
						traceablityNo: this.$route.query.traceablityNo,
					},
				}).then((d) => {
					if(d.status == 'success' && typeof d.result != 'string') {
						//console.log('ddd', d.result)
						if(TITLE == '宁夏金河科技股份有限公司') {
							if(new Date(d.result.varietyEntityView.expirationDate) < new Date()) {
								this.isContentShow = false;
							} else {
								this.isContentShow = true;
							}
						} else {
							this.isContentShow = true;
						}
						//判断基地信息
						if(d.result.farmEntityViewList == null || d.result.farmEntityViewList.length <= 0) {
							this.farmEntityView = false;
							this.qiyeStyle={
								width:'7rem'
							}
						} else {
							this.qiyeStyle={
								width:'3.3rem'
							}
							this.farmEntityView = true;
						}
						//判断实时动态
						if(d.result.newsEnityList != null && d.result.newsEnityList.length > 0) {
							this.isNoticeShow=true;
							this.newsEnityList=d.result.newsEnityList
						}
						
						//产品信息
						Object.assign(this.company, d.result.company)
						Object.assign(this.varietyEntityView, d.result.varietyEntityView)
						if(this.varietyEntityView.photo != null) {
							this.varietyEntityView.photo = IMG_URL + this.varietyEntityView.photo;
						} else {
							this.varietyEntityView.photo = logo;
							this.imgStyle={
								width:'1.42rem',
								height:'1.42rem'
							}
						}
						if(d.result.cultureProcessView.cultureEntityView.cultureId == null) {
							this.recoveryEntityViewShow = false; //收获信息
							this.cultureEntityViewShow = false; //档案判断
							this.processEntityListShow = false; //农事过程
						} else {
							this.recoveryEntityViewShow = true;
							this.cultureEntityViewShow = true;
							this.processEntityListShow = true;
							//收获信息
							Object.assign(this.recoveryEntityView, d.result.cultureProcessView.recoveryEntityView)
							if(this.recoveryEntityView.photo != null) {
								this.recoveryEntityView.photo = IMG_URL + this.recoveryEntityView.photo
							}
							//档案时间
							Object.assign(this.cultureEntityView, d.result.cultureProcessView.cultureEntityView)
							this.cultureEntityViewShow = true;
							//农事过程 processEntityList
							if(d.result.cultureProcessView.processEntityList == null || d.result.cultureProcessView.processEntityList.length <= 0) {
								this.processEntityListShow = false; //农事过程
							} else {
								this.processEntityList = d.result.cultureProcessView.processEntityList
								this.$nextTick(() => {
									this.farmSwiper();
								})
							}
						}
						//加工信息
						if(d.result.productInfor == null) {
							this.productInforShow = false;
						} else {
							Object.assign(this.productInfor, d.result.productInfor)
							if(this.productInfor.photo != null) {
								this.productInfor.photo = IMG_URL + this.productInfor.photo
							}
							this.productInforListProcess = d.result.productInfor.listProcess
							if(this.productInforListProcess.length > 0) {
								for(let m = 0; m < this.productInforListProcess.length; m++) {
									this['videoShowSwiperArr' + m] = []
									if(this.productInforListProcess[m].video) {
										if(this.productInforListProcess[m].video.indexOf("://") > -1) {
											this.productInforListProcess[m].video = this.productInforListProcess[m].video
										} else { //在线视频
											this.productInforListProcess[m].video = VIDEO_URL + this.productInforListProcess[m].video
										}
										this['videoShowSwiperArr' + m].push(this.productInforListProcess[m].video)
									}
									if(this.productInforListProcess[m].photo) {
										var arr = this.productInforListProcess[m].photo.split(',');
										for(let a = 0; a < arr.length; a++) {
											this['videoShowSwiperArr' + m].push(IMG_URL + arr[a])
										}
									}
								}
								this.$nextTick(() => {
									this.processSwiper();
									this.videoSwiper();
								})
							} else {
								this.productInforListProcessShow = false;
							}
						}
						//面食分享
						if(d.result.cookbooks != null && d.result.cookbooks.length > 0) {
							this.isCookedShow = true;
							this.cookbooks = d.result.cookbooks
							this.isPopupCookedShow=true;
							this.$nextTick(() => {
								this.cookedSwiper();
								this.popupCooked();
							})
						}
					} else {
						this.isContentShow = false; //false
						Toast(d.result);
					}
				})
			},

			//农事过程的Swiper
			farmSwiper() {
				var vm = this;
				var farmSwiper = new Swiper('.farmSwiper', {
					observeParents: true, //修改swiper的父元素时，自动初始化swiper
					paginationClickable: true,
					autoplay: 4000,
					lazyLoading: true,
					lazyLoadingOnTransitionStart: true,
					lazyLoadingInPrevNext: true,
					lazyLoadingInPrevNextAmount: 1,
					centeredSlides: true,
					effect: 'coverflow',
					slidesPerView: 2, //显示几个silder auto
					spaceBetween: -180, //间隔
					slidesOffsetBefore: 0, //左侧的偏移量
					coverflow: {
						rotate: 0, //两侧的翻转角度
						stretch: 0, //左右的间隔
						depth: 300, //后面的缩小好多
						modifier: 2,
						slideShadows: true
					},
					pagination: '.swiper-pagination-farm',
					paginationType: 'fraction',
					onInit: function(swiper) {
						var isOneImgStr = '';
						if(vm.processEntityList.length == 1) {
							isOneImgStr = '<img src="'
						} else {
							isOneImgStr = '<img data-src="'
						}
						for(var i = 0; i < vm.processEntityList.length; i++) {
							var isShowImg = 'none';
							if(vm.processEntityList[i].photo && vm.processEntityList[i].photo != null) {
								isShowImg = 'block'
							}
							//可以不用字符串，直接用v-for循环，这里当做练习
							var strLi = ''
							for(let a = 0; a < vm.processEntityList[i].processMaterielViewList.length; a++) {
								strLi += `<span>${vm.processEntityList[i].processMaterielViewList[a].breedName}、</span>`
							}
							var str = '<div class="swiper-slide">' +
								'<div class="content_header">' + vm.processName + '</div>' +
								'<div class="biaoqian">' + (i + 1) + '</div>' +
								'<ul>' +
								'<li>' + vm.processEntityList[i].processContent + '</li>' +
								'<li>' + vm.processEntityList[i].operatorDate + '</li>' +
								'<li style="margin-bottom: .12rem;display:' + isShowImg + '">' + isOneImgStr + IMG_URL + vm.processEntityList[i].photo + '" class="swiper-lazy" /> ' + '</li>' +
								'<li class="dian"><i></i>操作人：<span>' + vm.processEntityList[i].chargeName + '</span></li>' +
								'<li class="dian"><i></i>投放品：' + strLi + '</span></li>'
							'</ul>'
							'</div>'
							swiper.appendSlide(str);
						}
						//						swiper.slideTo(0);
					},
					onClick:function(swiper){
						console.log(swiper.activeIndex)
					}
				});
			},
			//弹出框的swiper
			videoShowSwiper() {
				var vm = this
				var videoShowSwiper = new Swiper('#videoShow', {
					observeParents: true,
					lazyLoading: true,
					centeredSlides: true,
					width: window.innerWidth, //innerHeight
					height: window.innerHeight,
					pagination: '.swiper-pagination-videoShow',
					onSlideChangeStart: function(swiper) {
						Object.assign(vm.videoShowSwiperObj, swiper)
					},
					paginationType: 'fraction',
					onInit: function(swiper) {
						//						if(swiper.slides.length > 0) {
						//							for(var i = 0; i < 2; i++) {
						//								swiper.slides[i].style.transform = 'translate3d(' + ((2 - 1) * 0.5) * swiper.width + 'px, 0px, 0px)';
						//							}
						//						}

						//						swiper.slideTo(0);
					},
				});
				Object.assign(this.videoShowSwiperObj, videoShowSwiper)
			},
			removeSwiper() {
				this.videoShowSwiperObj.removeAllSlides();
			},
			//视频和图的Swiper
			videoSwiper() {
				var vm = this;
				var videoSwiper = new Swiper('.videoSwiper', {
					//					autoplay: 5000,
					pagination: '.swiper-pagination-video',
					onClick: function(swiper) {
						vm.videoSwiperOnclick(swiper)
					}
				});
			},
			//判断是否是tap事件
			videoSwiperOnclick(swiper) {
				var vm = this;
				var count = null;
				if(vm.appendArr.length == 1) {
					count = 2
				}
				vm.removeSwiper();
				vm.popupShow = true;
				vm.videoShowSwiperObj.appendSlide(vm.appendArr);

				//1||0.5  2||0.5  3||1  4||1.5
				//				if(swiper.activeIndex == 1) {
				//					alert('说')
				//					vm.videoShowSwiperObj.slideTo(swiper.activeIndex);
				//					for(var i = 0; i < vm.videoShowSwiperObj.slides.length; i++) {
				//						vm.videoShowSwiperObj.slides[i].style.transform = 'translate3d(' + ((2 - 1) * 0.5) * vm.videoShowSwiperObj.width + 'px, 0px, 0px)';
				//					}
				//				} else {
				//如果重零开始这里就要多触发一次，
				count = vm.appendArr.length
				vm.videoShowSwiperObj.slideTo(1);
				vm.videoShowSwiperObj.slideTo(swiper.activeIndex);
				for(var i = 0; i < vm.videoShowSwiperObj.slides.length; i++) {
					vm.videoShowSwiperObj.slides[i].style.transform = 'translate3d(' + ((count - 1) * 0.5) * vm.videoShowSwiperObj.width + 'px, 0px, 0px)';
				}
				//				}
				vm.$nextTick(function() {
					var video = document.getElementById("videoShowId");
					if(video != null) {
						video.pause();
					}
				})
			},
			//加工信息里面加工过程的swiper
			processSwiper() {
				var vm = this;
				var process = new Swiper('#process', {
					autoplay: 4000,
					observer: true,
					observeParents: true, //修改swiper的父元素时，自动初始化swiper
					paginationClickable: true,
					lazyLoading: true,
					lazyLoadingOnTransitionStart: true,
					lazyLoadingInPrevNext: true,
					lazyLoadingInPrevNextAmount: 1,
					centeredSlides: true, //不居中的话那个swiper.activeIndex有问题
					slidesPerView: 'auto', //显示几个silder auto
					spaceBetween: 15, //间隔
					slidesOffsetBefore: 0, //左侧的偏移量
					pagination: '.swiper-pagination-farm',
					paginationType: 'fraction',
					initialSlide: 0,
					onInit: function(swiper) {
						vm.showSwiperAppendArr(swiper)
					},
					onSlideChangeStart: function(swiper) {
						vm.showSwiperAppendArr(swiper)
					}
				});
			},
			//初始化videoShowSwiper appendArr
			showSwiperAppendArr(swiper) {
				var vm = this
				vm.count = swiper.activeIndex;
				vm.appendArr = [];
				var videoArr = vm['videoShowSwiperArr' + swiper.activeIndex]
				for(let m = 0; m < videoArr.length; m++) {
					var str = '';
					if(videoArr[m].indexOf('.jpg') > 0 || videoArr[m].indexOf('.JPG') > 0 ||
						videoArr[m].indexOf('.png') > 0 || videoArr[m].indexOf('.PNG') > 0 ||
						videoArr[m].indexOf('.jepg') > 0 || videoArr[m].indexOf('.JEPG') > 0) {
						str += `<div  class="swiper-slide">
									<img src=${videoArr[m]} alt="" />
									</div>`

					} else {
						//autoplay="autoplay"
						str = `<div  class="swiper-slide">
									<video  id='videoShowId' poster=${videoImg}   controls="controls" >
										<source src=${videoArr[m]} type="video/mp4" />  
										<source src=${videoArr[m]} type="video/ogg">
										您的浏览器不支持 HTML5 video 标签。
									</video></div>`
					}
					vm.appendArr.push(str)
				}
			},
			//面食分享的swiper
			cookedSwiper() {
				var vm = this
				var cookedSwiper = new Swiper('.cookedSwiper', {
					observeParents: true,
					lazyLoading: true,
					centeredSlides: true,
					lazyLoadingOnTransitionStart: true,
					lazyLoadingInPrevNext: true,
					lazyLoadingInPrevNextAmount: 1,
					autoplay: 4000,
					pagination: '.swiper-pagination-cooked',
					onSlideChangeStart: function(swiper) {

					},
					paginationType: 'fraction',
					onInit: function(swiper) {
						
					},
					onClick:function(swiper){
						vm.isPopupCookedShow=true;
						vm.popupCookedSwiperObj.slideTo(swiper.activeIndex)
						vm.popupCookedShow = true;
					}
				});
			},
			//面食分享弹出
			popupCooked(){
				var vm = this
				var popupCooked = new Swiper('.popupCooked', {
					observeParents: true,
					lazyLoading: true,
					width: window.innerWidth,
					centeredSlides: true,
					lazyLoadingOnTransitionStart: true,
					lazyLoadingInPrevNext: true,
					lazyLoadingInPrevNextAmount: 1,
					pagination: '.swiper-pagination-cooked',
					paginationType: 'fraction',
					onInit: function(swiper) {
						var length=0;
						if(vm.cookbooks[0].video!=null){
							length+=1
						}
						if(vm.cookbooks[0].photo!=null){
							length+=vm.cookbooks[0].photo.split(',').length
						}
						for(let i = 0; i < length; i++) {
							swiper.slides[i].style.transform = 'translate3d(' + ((length - 1) * 0.5) * swiper.width + 'px, 0px, 0px)';
						}
					},
				});
				Object.assign(this.popupCookedSwiperObj,popupCooked)
			},
			noticeClick(){
				this.$router.push({name:'news',query:{newsId:this.newsEnityList[0].newsId}})
			},
			titleJudge(){
				if(TITLE == '宁夏伊聚徳农工贸有限公司' || TITLE == '宁夏金河科技股份有限公司') {
					this.processName = '养殖过程'
					this.messageName = '收获信息'
					this.record = '养殖档案'
				}
				if(TITLE == '宁夏金河科技股份有限公司') {
					this.cookbooksName='金河产品分享'
					this.isJinheImgShow = true;
					this.messageName = '收奶信息'
					this.bannerStyle = {
						backgroundImage: `url(${jinhe})`,
						backgroundSize: 'contain'
					}
				} else if(TITLE == '灵武市玉锋粮油综合加工有限公司') {
					this.bannerStyle = {
						backgroundImage: `url(${yufeng})`,
						backgroundSize: 'contain'
					}
				} else if(TITLE == '宁夏广玉面粉有限公司') {
					this.cookbooksName='广玉面食分享'
					this.bannerStyle = {
						backgroundImage: `url(${guangyu})`,
						backgroundSize: 'contain'
					}
				} else if(TITLE == '宁夏贺丰种业有限公司') {
					this.bannerStyle = {
						backgroundImage: `url(${hefeng})`,
						backgroundSize: 'contain'
					}
				} else if(TITLE == '宁夏宁宝世家食品科技有限公司') {
					this.bannerStyle = {
						backgroundImage: `url(${ningbao})`,
						backgroundSize: 'contain'
					}
				} else if(TITLE == '宁夏万家香食品有限公司') {
					this.bannerStyle = {
						backgroundImage: `url(${wanjia})`,
						backgroundSize: 'contain'
					}
				} else if(TITLE == '宁夏伊聚徳农工贸有限公司') {
					this.bannerStyle = {
						backgroundImage: `url(${yijude})`,
						backgroundSize: 'contain'
					}
				} else if(TITLE == '银川育新枸杞种业有限公司') {
					this.bannerStyle = {
						backgroundImage: `url(${yuxin})`,
						backgroundSize: 'contain'
					}
				} else {
					this.bannerStyle = {
						backgroundImage: `url(${bannerOld})`,
						backgroundSize: 'contain'
					}
				}
			},
		}
	}
</script>

<style lang="scss">

</style>