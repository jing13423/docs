<template>
	<div class="integration-detail">
		<section class="integration-detail-banner">
			<img class="no-zoom" src="~@theme/assets/images/integration/detail-banner.png" />
			<h1>{{ this.$frontmatter.bannerTitle }}</h1>
		</section>

		<div class="integration-detail-main-content content-layout-container content-layout-container__without-sidebar">
			<AuthingSteps @step-click="setStep" :steps="steps" :currentStep="currStep" />

			<Content
				:style="{
					minHeight: '500px'
				}"
				:pageKey="stepComponentKey"
			/>

			<div class="integration-detail-footer">
				<RouterLink :to="backLink">
					<ArrowRight
						:style="{
							transform: 'rotate(180deg)',
							marginRight: '4px'
						}"
					/>
					回到列表
				</RouterLink>
				<div class="integration-detail-btn-container">
					<AuthingButton :disabled="isFirstStep" @click="handlePrev">上一步</AuthingButton>
					<AuthingButton
						@click="handleNext"
						:style="{
							marginLeft: '24px'
						}"
						type="primary"
						>{{ isLastStep ? '我知道了，返回列表' : '下一步' }}</AuthingButton
					>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import AuthingSteps from '@theme/components/AuthingSteps/index.vue';
import AuthingButton from '@theme/components/AuthingButton/index.vue';
import ArrowRight from '@theme/components/Icons/ArrowRight.vue';

export default {
	name: 'IntegrationDetail',
	props: {
		backLink: {
			type: String,
			default: '/integration/'
		}
	},
	components: {
		ArrowRight,
		AuthingSteps,
		AuthingButton
	},
	data() {
		return {
			currStep: 0
		};
	},
	computed: {
		steps() {
			return this.$frontmatter.steps;
		},
		isFirstStep() {
			return this.currStep === 0;
		},
		isLastStep() {
			return this.currStep === this.steps.length - 1;
		},
		stepComponentKey() {
			return this.$site.pages.find(item => {
				return item.regularPath === `${this.$page.regularPath}${this.currStep}.html`;
			}).key;
		}
	},
	watch: {
		$route: {
			immediate: true,
			handler() {
				const queryStep = Number(this.$route.query.step) || 0;

				this.currStep = Math.min(queryStep, this.steps.length - 1);
			}
		}
	},
	methods: {
		handlePrev() {
			if (this.isFirstStep) {
				return;
			}

			this.setStep(this.currStep - 1);
		},
		handleNext() {
			if (this.isLastStep) {
				this.$router.push(this.backLink);
				return;
			}

			this.setStep(this.currStep + 1);
		},

		setStep(step) {
			this.$router.replace({
				query: {
					step
				}
			});
		}
	}
};
</script>

<style lang="stylus">
.integration-detail {
  a {
    word-break: break-all;
  }
  margin-top: 1.55rem!important;
  .integration-detail-main-content {
    margin: 0 auto;
  }
  .integration-detail-footer {
    align-items: center;
    margin-top: 56px;
    margin-bottom: 100px;
    display: flex;
    justify-content: space-between;
    a {
      display: flex;
      align-items: center;
      font-size: 14px;
    }
  }
  .integration-detail-banner {
    height: 215px;
    background-color: #F6F7F8;
    margin-bottom: 67px;
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    img {
      height: 100%;
      position: absolute;
      object-fit: contain;
    }
    h1 {
      z-index: 1;
      font-size: 36px;
      font-weight: 500;
      color: #111111;
      line-height: 50px;
      text-align: center;
    }
  }
}
@media (max-width: $MQMobile) {
  .integration-detail {
    .integration-detail-banner {
      height: 10.875rem;
      h1 {
        font-size 1.75rem;
        width: 14.1875rem;
        line-height: 1.4;
      }
    }
    .integration-detail-footer {
      flex-wrap: wrap;
    }
    .integration-detail-btn-container {
      width: 100%;
      order: -1;
      display: flex;
      justify-content: space-between;
      margin-bottom: 1rem;
    }
  }
}
</style>
