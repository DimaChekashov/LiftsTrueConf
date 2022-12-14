<template>
    <div class="elevator">
      <div class="elevator__lifts">
        <lift 
          v-for="liftItem in lifts" 
          :key="liftItem.id + '-lift'" 
          :boothHeight="floorHeight"
          :data="liftItem"
          @setStatus="setLiftStatus"
        />
      </div>

      <div class="elevator__floors">
        <div 
          v-for="floor in floors" 
          class="elevator__floor" 
          :key="floor + '-floor'" 
          :style="{ 'height': `${floorHeight}px` }"
        >
          <div class="elevator__controlls">
            <div class="elevator__title">{{floor}}</div>
            <button 
              class="elevator__btn" 
              :class="floorCalledBtns.has(floor) ? 'active' : ''"
              :onClick="() => moveTo(floor)" 
            />
          </div>
        </div>
      </div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Lift from "./Lift.vue";

type LiftStatusType = "ready" | "moving" | "waiting";

interface Lift {
  id: number;
  status: LiftStatusType;
  position: number;
}

interface Data {
  floorHeight: number;
  queue: number[];
  lifts: Lift[];
  floorCalledBtns: Set<number>;
}


export default defineComponent({
  name: "Elevator",
  components: { Lift },
  data(): Data {
    return {
      floorHeight: (window.innerHeight - 30) / this.floors,
      queue: [],
      lifts: Array.from({ length: this.liftsAmount }, (_, i) => ({
        id: i,
        status: "ready",
        position: 1
      })),
      floorCalledBtns: new Set()
    }
  },
  props: {
    floors: {
      type: Number,
      default: 5,
    },
    liftsAmount: {
      type: Number,
      default: 3,
    },
  },
  methods: {
    moveTo(position: number) {
      if(!this.lifts.some(lift => lift.position === position)) {
        this.floorCalledBtns.add(position);
        this.queue.push(position);
        this.checkQueue();
      }
    },
    checkQueue() {
      const liftIndex = this.lifts.findIndex(lift => lift.status === "ready");
      
      if (liftIndex !== -1 && this.queue.length > 0) {
        this.lifts[liftIndex] = { ...this.lifts[liftIndex], position: this.queue.shift()! }
      }
    },
    setLiftStatus(lift: Lift) {
      this.lifts[lift.id] = lift;
      if (lift.status === "waiting") {
        this.floorCalledBtns.delete(lift.position);
      }
      this.checkQueue();
    }
  }
});
</script>

<style scoped lang="scss">
.elevator {
  display: flex;
  gap: 30px;
  &__floors {
    width: 100%;
    display: flex;
    flex-direction: column-reverse;
  }
  &__floor {
    position: relative;
    &:last-child {
      &::before {
        content: "";
        display: inline-block;
        height: 1px;
        width: calc(100vw - 30px);
        background-color: rgb(171, 171, 171);
        position: absolute;
        right: 0;
        top: 0;
      }
    }
    &::after {
      content: "";
      display: inline-block;
      height: 1px;
      width: calc(100vw - 30px);
      background-color: rgb(171, 171, 171);
      position: absolute;
      right: 0;
      bottom: 0;
    }
  }
  &__lifts {
    display: flex;
    gap: 20px;
  }
  &__controlls {
    padding: 15px 0;
  }
  &__btn {
    width: 30px;
    height: 30px;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    &::after {
      content: "";
      width: 12px;
      height: 12px;
      background-color: #000;
      border: 3px solid #fff;
      outline: 1px solid #000;
      border-radius: 50%;
    }
    &.active {
      border-color: red;
      &::after {
        background-color: red;
        outline-color: red;
      }
    }
  }
  &__title {
    font-size: 18px;
    margin-bottom: 10px;
  }
}
</style>
