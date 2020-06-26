<template>
  <div class="home">
    <div class="wrapper">
      <van-collapse v-model="activeNames">
        <van-collapse-item v-for="(item,index) in tree" :key="index" :name="index">
          <template #title>
            <div
              class="auto"
              :class="{'van-checkbox-indeterminate': !item.checked && item.indeterminate}"
              @click.stop
            >
              <van-checkbox
                v-model="item.checked"
                shape="suqare"
                icon-size="14px"
                @change="handleFather(index)"
              >{{item.name}}</van-checkbox>
            </div>
          </template>
          <div class="child" v-for="(child,childIndex) in item.children" :key="childIndex">
            <van-checkbox
              v-model="child.checked"
              shape="suqare"
              icon-size="14px"
              @click="handleChild(index)"
            >{{child.name}}</van-checkbox>
            <van-divider></van-divider>
          </div>
        </van-collapse-item>
      </van-collapse>
    </div>
    <div class="footer-btn">
      <div class="check-all">
        <van-checkbox v-model="isCheckAll" shape="square" icon-size="14px" @click="handleAll">全选</van-checkbox>
      </div>
      <div class="is-checked" @click="viewSelected">
        <span class="line"></span>
        <span class="text">已选</span>
        <span>{{isSelected.length}}</span>
        <span>人</span>
      </div>
      <van-button type="primary">确定</van-button>
    </div>
    <!-- van-popup -->
    <van-popup v-model="showFlag" position="bottom" :style="{ 'max-height': '50%' }">
      <div class="popup-content">
        <van-cell v-for="(item, index) in isSelected" :key="index" :title="item.name" :value="item.name" size="large" />
      </div>
    </van-popup>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",
  data() {
    return {
      activeNames: [],
      tree: [
        {
          id: 1,
          name: "总经理",
          checked: false,
          children: [
            {
              id: 1,
              name: "张三"
            }
          ]
        },
        {
          id: 2,
          name: "总务室",
          children: [
            {
              id: 1,
              name: "李四"
            },
            {
              id: 2,
              name: "王五"
            },
            {
              id: 3,
              name: "法外狂徒"
            }
          ]
        },
        {
          id: 3,
          name: "保安室",
          children: [
            {
              id: 1,
              name: "马六"
            }
          ]
        }
      ],
      isCheckAll: false,
      showFlag: false
    };
  },
  computed: {
    isSelected() {
      let arr = [];
      this.tree.forEach(item => {
        if (item.children) {
          item.children.forEach(child => {
            if (child.checked) {
              arr.push(child);
            }
          });
        }
      });
      return arr;
    }
  },
  created() {
    let arr = JSON.parse(JSON.stringify(this.tree));
    this.recursive(arr);
    this.tree = arr;
    console.log(this.tree);
  },
  methods: {
    recursive(arr) {
      arr.forEach(item => {
        item.checked = false;
        item.indeterminate = false;
        if (item.children) {
          item.children.forEach(child => {
            child.checked = false;
          });
        }
      });
      return arr;
    },
    handleFather(index) {
      let arr = JSON.parse(JSON.stringify(this.tree));
      if (arr[index].checked) {
        // 当前全选
        arr[index].indeterminate = false;
        if (arr[index].children) {
          arr[index].children.forEach(item => {
            item.checked = true;
          });
        }
      } else {
        // 没有全选
        // 判断当前是否为半选
        if (arr[index].indeterminate) {
          // 半选
        } else {
          // 无选中
          arr[index].indeterminate = false;
          if (arr[index].children) {
            arr[index].children.forEach(item => {
              item.checked = false;
            });
          }
        }
      }
      this.tree.splice(index, 1, arr[index]);
      console.log("this.tree", this.tree);
      this.isCheckAll = this.tree.every(item => {
        return item.checked;
      });
    },
    handleChild(index) {
      // index 为当前层在tree中的下标
      let arr = JSON.parse(JSON.stringify(this.tree));
      let check_status = arr[index].children.every(item => {
        return item.checked;
      });
      if (check_status) {
        // 表示当前层child全部被选中，则当前层应当为checked
        arr[index].checked = true;
        arr[index].indeterminate = false;
      } else {
        let child_check_status = arr[index].children.some(item => {
          return item.checked;
        });
        if (child_check_status) {
          arr[index].checked = false;
          arr[index].indeterminate = true;
        } else {
          arr[index].checked = false;
          arr[index].indeterminate = false;
        }
      }
      this.tree.splice(index, 1, arr[index]);
    },
    handleAll() {
      let arr = this.tree;
      if (this.isCheckAll) {
        arr.forEach(item => {
          item.checked = true;
          item.indeterminate = false;
          if (item.children) {
            item.children.forEach(child => {
              child.checked = true;
            });
          }
        });
      } else {
        arr.forEach(item => {
          item.checked = false;
          item.indeterminate = false;
          if (item.children) {
            item.children.forEach(child => {
              child.checked = false;
            });
          }
        });
      }
    },
    // 查看已选
    viewSelected() {
      this.showFlag = !this.showFlag;
    }
  }
};
</script>

<style lang="scss">
.home {
  box-sizing: border-box;
  min-height: 100vh;
  background-color: #f5f5f5;
  .van-divider {
    margin: 0;
  }
  .wrapper {
    box-sizing: border-box;
  }
  .van-collapse-item {
    margin-bottom: 9px;
    .van-cell__title {
      display: flex;
      .auto {
        flex: 0 0 auto;
      }
    }
    .van-collapse-item__content {
      padding: 0 16px;
      .child {
      }
      .van-checkbox {
        height: 40px;
      }
    }
    .van-checkbox-indeterminate {
      .van-icon {
        color: #fff;
        background-color: #1989fa;
        border-color: #1989fa;
        overflow: hidden;
      }
      .van-icon:before {
        content: "";
        width: 100%;
        height: 100%;
        background-color: #fff;
        transform: scaleX(0.5) scaleY(0.2);
      }
    }
  }
  .footer-btn {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: #fff;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .check-all {
      padding: 0 16px;
    }
    .is-checked {
      flex: 1;
      display: flex;
      align-items: center;
      line-height: 20px;
      .line {
        background-color: #eee;
        width: 2px;
        height: 24px;
        transform: scaleX(0.5);
      }
      .text {
        padding: 0 9px;
      }
    }
  }
  .popup-content {
    padding: 12px 16px;
  }
}
</style>
