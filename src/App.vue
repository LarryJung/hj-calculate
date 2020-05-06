<template>
  <div id="app">
    <img id="grandmother" alt="grandmother" src="./assets/grandmother.png" />
    <h1>"학점을 알려줌마"</h1>
    <template v-if="loading">
      <div>학점 계산 중 입니다.</div>
      <div>{{ranNum}}</div>
    </template>
    <template v-else-if="isResult">
      <div>당신의 이번 학기 평점은 "{{point}}" 입니다.</div>
      <button @click="reload()">다시하기</button>
    </template>
    <template v-else>
      <p>아래 문항에 빠짐없이 진심으로 답해주세요.</p>
      <h3>{{this.groups[this.currentGroup].name}} ({{this.currentGroup + 1}}/4)</h3>
      <div id="question-box">
        <template v-if="currentGroup == 2">
          <div v-for="item in this.groups[this.currentGroup].questions">
            <input
              type="checkbox"
              v-bind:value="item"
              v-bind:name="item.name"
              v-model="multiPickedList"
            />
            <label>{{item.name}}</label>
          </div>
        </template>
        <template v-else>
          <div v-for="item in this.groups[this.currentGroup].questions">
            <input type="radio" v-bind:value="item" v-bind:name="currentGroup" v-model="picked" />
            <label>{{item.name}}</label>
          </div>
        </template>
      </div>
      <div>
        <button @click="goNextGroup()">다음</button>
      </div>
      <!-- <div>선택함: {{picked}}</div>
      <div>선택한 총 weight: {{pickeds}}</div>
      <div>pickedLis: {{multiPickedList}}</div>-->
    </template>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld
  },
  data() {
    return {
      loading: false,
      isResult: false,
      currentGroup: 0,
      picked: {},
      pickeds: [],
      multiPickedList: [],
      point: "",
      ranNum: 0,
      groups: [
        {
          num: 0,
          name: "지난 학기는 어땠습니까?",
          questions: [
            {
              name: "나는 지난 학기 학점이 4 ~ 4.5 사이였다.",
              weight: 4.25
            },
            {
              name: "나는 지난 학기 학점이 3.5 ~ 4 사이였다.",
              weight: 3.75
            },
            {
              name: "나는 지난 학기 학점이 3 ~ 3.5 사이였다.",
              weight: 3.25
            },
            {
              name: "나는 지난 학기 학점이 2.5 ~ 3 사이였다.",
              weight: 2.75
            },
            {
              name: "나는 지난 학기 학점이 2.5 이하였다.",
              weight: 2
            },
            {
              name: "나는 이번 학기가 처음이다.",
              weight: 3.5
            }
          ],
          calcFun: value => value
        },
        {
          num: 1,
          name: "지난 방학 때는 무엇을 했는지요?",
          questions: [
            {
              name: "취업을 위한 영어 공부에 주력했다.",
              weight: 3.8
            },
            {
              name: "생계를 위해 아르바이트 위주로 보냄. 따로 공부는 잘 못함.",
              weight: 3.5
            },
            {
              name: "이전 학기 복습을 조금이라도 했다.",
              weight: 4
            },
            {
              name: "다음 학기 예습을 조금이라도 해봤다.",
              weight: 4.3
            },
            {
              name: "별 다른 생각이 없다.",
              weight: 3
            },
            {
              name:
                "학기 준비를 하려고 했지만 쉬는 방학을 만끽하는데 집중했음.",
              weight: 3
            }
          ],
          calcFun: value => value
        },
        {
          num: 2,
          name: "현재 학교에서의 모습은 어떠합니까? (다수 선택 가능)",
          questions: [
            {
              name: "나는 롤에 중독되어 있다.",
              weight: 3.33
            },
            {
              name: "이성친구가 있다.",
              weight: 3.83
            },
            {
              name: "주식투자 동아리에 가입했다.",
              weight: 3.28
            },
            {
              name: "한강이 근처에 있다.",
              weight: 3.74
            },
            {
              name: "이번 학기 책을 모두 마련했다.",
              weight: 4.12
            },
            {
              name: "야구를 매일 챙겨본다",
              weight: 3.88
            },
            {
              name: "통학이 3시간 이상 걸린다.",
              weight: 3.67
            },
            {
              name: "자신 없는 과목이 3과목 이상 있다.",
              weight: 3.03
            },
            {
              name: "포기하고 싶은 과목이 있다.",
              weight: 3.19
            },
            {
              name: "나는 학점보다 알바가 더 중요하다.",
              weight: 3.1
            },
            {
              name: "내가 무슨 과목을 수강했는지 지금 당장 말할 수 있다.",
              weight: 4.35
            },
            {
              name: "나를 좋아하는 교수님이 두 분 이상 계신다.",
              weight: 4
            }
          ],
          calcFun: arr => arr.reduce((a, b) => a + b) / arr.length
        },
        {
          num: 3,
          name: "마지막 마음가짐을 물어보겠습니다.",
          questions: [
            {
              name: "시험 시간 도서관에서 밤 샐 각오가 있습니다!",
              weight: 4.26
            },
            {
              name: "열심히 하겠다만 성적에 연연하지 않겠습니다.",
              weight: 3.87
            },
            {
              name: "휴강(또는 휴학)을 생각하고 있습니다.",
              weight: 3
            },
            {
              name: "별 생각이 없습니다..",
              weight: 2.84
            },
            {
              name: "나는 믿는 구석이 있습니다.",
              weight: 3.6
            }
          ],
          calcFun: value => value
        }
      ]
    };
  },
  methods: {
    goNextGroup() {
      if (this.currentGroup === 2) {
        const avg = this.groups[this.currentGroup].calcFun(
          this.multiPickedList.map(l => l.weight)
        );
        this.pickeds.push(avg);
        this.multiPickedList = [];
      } else {
        if (!this.picked.weight) {
          alert("하나는 선택해야 합니다.");
          return;
        }
        this.pickeds.push(this.picked.weight);
        this.picked = {};
      }
      if (this.currentGroup === 3) {
        this.showResult();
        return;
      }
      this.currentGroup = this.currentGroup + 1;
    },
    showResult() {
      this.loading = true;

      const vm = this;

      setTimeout(function() {
        vm.loading = false;
        vm.isResult = true;
        vm.pickeds.push(Math.random() + 3.5);
        vm.point = (
          vm.pickeds.reduce((a, b) => a + b) / vm.pickeds.length
        ).toPrecision(3);
      }, 3000);

      setInterval(function() {
        vm.ranNum = Math.floor(1 + Math.random() * 6);
      }, 30);
    },
    reload() {
      window.location.reload();
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #f9f9f9;
  padding-bottom: 400px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

#grandmother {
  max-width: 35%;
}

#question-box {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  /* justify-content: flex-start; */
}
</style>
