# JS-mini-store

一个基于事件的全局状态管理工具。

# 如何使用呢？

## 1、npm 安装依赖

```shell
npm install js-mini-store
```

## 2、事件总线（event-bus）

```js
const { JSEventBus } = require("js-mini-store");

const eventBus = new JSEventBus();

const whyCallback1 = (...payload) => {
  console.log("whyCallback1:", payload);
};

const whyCallback2 = (...payload) => {
  console.log("whyCallback1:", payload);
};

const lileiCallback1 = (...payload) => {
  console.log("lileiCallback1:", payload);
};

eventBus.on("why", whyCallback1);
eventBus.on("why", whyCallback2);
eventBus.on("lilei", lileiCallback1);
eventBus.once("why", (...payload) => {
  console.log("why once:", payload);
});

setTimeout(() => {
  eventBus.emit("why", "abc", "cba", "nba");
  eventBus.emit("lilei", "abc", "cba", "nba");
}, 1000);

setTimeout(() => {
  eventBus.off("why", whyCallback1);
  eventBus.off("lilei", lileiCallback1);
}, 2000);

setTimeout(() => {
  eventBus.emit("why");
  eventBus.emit("lilei");
}, 3000);
```

## 3、数据共享（event-store）

```js
const { JSEventStore } = require("js-mini-store");
const axios = require("axios");

const eventStore = new JSEventStore({
  state: {
    name: "why",
    friends: ["abc", "cba", "nba"],
    banners: [],
    recommends: [],
  },
  actions: {
    getHomeMultidata(ctx) {
      console.log(ctx);
      axios.get("http://123.207.32.32:8000/home/multidata").then((res) => {
        const banner = res.data.data.banner;
        const recommend = res.data.data.recommend;
        // 赋值
        ctx.banners = banner;
        ctx.recommends = recommend;
      });
    },
  },
});

// 数据监听
eventStore.onState("name", (value) => {
  console.log("监听name:", value);
});

eventStore.onState("friends", (value) => {
  console.log("监听friends:", value);
});

eventStore.onState("banners", (value) => {
  console.log("监听banners:", value);
});

eventStore.onState("recommends", (value) => {
  console.log("监听recommends", value);
});

// 数据变化
setTimeout(() => {
  eventStore.setState("name", "lilei");
  eventStore.setState("friends", ["kobe", "james"]);
}, 1000);

eventStore.dispatch("getHomeMultidata");
```
