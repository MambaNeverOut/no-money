<template>
  <Layout class-prefix="editLabel">
    <header class="header">
      <ul class="tabs">
        <li
          class="tabs-item"
          v-for="item in recordTypeList"
          :key="item.value"
          :class="liClass(item)"
          @click="selectType(item)"
        >
          {{ item.text }}
        </li>
      </ul>
    </header>
    <main class="tags-content">
      <label class="amount">
        <span class="name">
          <span class="icon-wrapper">
            <Icon :name="record.tag.name"></Icon>
          </span>
          <span class="tagName">{{ record.tag.value }}</span>
        </span>
        <input
          type="text"
          readonly
          :value="record.amount"
          @focus="showNumberPad"
        />
      </label>
      <Tags
        :type="record.type"
        :selectedTag="selectedTag"
        @update:selectedTag="onUpdateTag"
      ></Tags>
      <NumberPad
        :show="show"
        :amount.sync="record.amount"
        :notes.sync="record.notes"
        @submit="saveRecord"
        @update:hide="hideNumberPad"
      ></NumberPad>
    </main>
  </Layout>
</template>

<script lang="ts">
import Vue from "vue";
import { Component } from "vue-property-decorator";
import dayjs from "dayjs";
import Tags from "@/components/EditLabel/Tags.vue";
import NumberPad from "@/components/EditLabel/NumberPad.vue";
import recordTypeList from "@/constants/recordTypeList";
import { expenseTags, incomeTags } from "@/constants/tagList";

@Component({ components: { Tags, NumberPad } })
export default class EditLabel extends Vue {
  show = "hide";
  record: RecordItem = {
    tag: { name: "food", value: "餐饮" },
    notes: "",
    type: "-",
    amount: 0
  };
  selectedTag = { name: "food", value: "餐饮" };
  recordTypeList = recordTypeList;

  created() {
    this.$store.commit("fetchRecords");
    this.$store.commit("fetchPunch");
  }
  liClass(item: RecordTypeItem) {
    return {
      selected: item.value === this.record.type
    };
  }
  selectType(item: RecordTypeItem) {
    this.record.type = item.value;
    this.record.tag = this.selectedTag =
      this.record.type === "-"
        ? { name: "food", value: "餐饮" }
        : { name: "pay", value: "工资" };
  }
  onUpdateTag(tag: Tag) {
    this.record.tag = tag;
  }
  showNumberPad() {
    this.show = "show";
  }
  hideNumberPad() {
    this.show = "hide";
  }
  saveRecord() {
    this.$store.commit("createRecord", this.record);
    if (this.$store.state.createRecordError === null) {
      window.alert("已保存");
    }
    let maxContinuousPunch: number = 1;
    if (this.$store.state.continuousPunch === 0) {
      this.$store.commit("setPunch");
    } else {
      const currentTime = dayjs(dayjs(new Date()).format("YYYY/MM/DD")).unix();
      const array = this.$store.state.recordList.map((i: RecordItem) =>
        dayjs(i.createdAt).unix()
      );
      if (array.indexOf(currentTime - 86400) >= 0) {
        this.$store.commit("setPunch");
        maxContinuousPunch = this.$store.state.continuousPunch;
      } else {
        this.$store.state.continuousPunch = maxContinuousPunch;
        maxContinuousPunch = 1;
      }
    }
    this.record.amount = 0;
    this.record.notes = "";
    this.hideNumberPad();
  }
}
</script>

<style lang="scss" scoped>
.editLabel-wrapper {
  overflow: hidden;
}
.header {
  position: absolute;
  top: 0;
  width: 100vw;
  display: flex;
  justify-content: center;
  background: #403f67;
  padding: 16px;
  font-size: 14px;
  color: white;
  button {
    border: none;
    background: inherit;
  }
  .tabs {
    display: flex;
    li {
      padding: 3px 16px;
      border: 1px solid white;
      &:first-child {
        border-top-left-radius: 8px;
        border-bottom-left-radius: 8px;
      }
      &:last-child {
        border-top-right-radius: 8px;
        border-bottom-right-radius: 8px;
      }
    }
  }
  .tabs-item.selected {
    color: black;
    background: #fefefe;
  }
}
.tags-content {
  margin-top: 60.8px;
  background: #f0f3f6;
  display: flex;
  .amount {
    position: absolute;
    background: #fefefe;
    width: 100%;
    padding: 8px;
    display: flex;
    justify-content: space-between;
    .name {
      display: flex;
      justify-content: center;
      .icon-wrapper {
        width: 36px;
        height: 36px;
        margin: 8px;
        display: flex;
        align-items: center;
        justify-content: center;
        > .icon {
          width: 30px;
          height: 30px;
        }
      }
      > .tagName {
        margin: auto 0;
      }
    }
    > input {
      text-align: right;
      margin-right: 16px;
      background: transparent;
      border: none;
    }
  }
  .tags {
    margin-top: 76px;
    flex-grow: 1;
  }
}
</style>
