<template>
  <div class="content">
    <!-- 标头 -->
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0,user-scalable=no"
    />
    <el-menu
      :default-active="activeIndex"
      class="el-menu-demo"
      mode="horizontal"
      :ellipsis="false"
      @select="handleSelect"
      active-text-color="#0ea27e"
      text-color="#0ea27e"
    >
      <el-menu-item>
        <img
          style="width: 45px"
          src="../asserts/openAi.jpg"
          alt="Element logo"
          @click="acc"
        />
      </el-menu-item>
      <div class="flex-grow" />
      <el-menu-item index="1">
        <a href="https://github.com/Yanyutin753/PandoraNext-TokensTool"
          >TokensTool地址</a
        >
      </el-menu-item>
      <el-menu-item index="2">
        <a href="https://chat.openai.com/api/auth/session">OpenAI官网</a>
      </el-menu-item>
      <el-menu-item index="3">
        <a href="https://github.com/pandora-next/deploy">Pandora地址</a>
      </el-menu-item>
      <el-sub-menu index="4">
        <template #title>系统设置</template>
        <el-menu-item index="4-1" @click="AgainPandora"
          >重启PandoraNext</el-menu-item
        >
        <el-menu-item index="4-2" @click="logout">退出登录</el-menu-item>
      </el-sub-menu>
    </el-menu>
    <div style="display: block; transform: translate(5vw, 2.5vh); width=95vw;">
      <el-page-header :icon="null" title=" ">
        <template #content>
          <div class="flex items-center">
            <el-avatar :size="iconSize" class="mr-3" :src="image" />
            <span class="text-large font-600 mr-3">PandoraNext</span>
            <span
              class="text-sm mr-2"
              style="color: var(--el-text-color-regular)"
            >
              TokensTool
            </span>
            <el-tag>made by Yang</el-tag>
          </div>
        </template>
      </el-page-header>
    </div>

    <!-- 添加token按钮 -->
    <van-floating-bubble
      v-model:offset="offset_task"
      axis="xy"
      icon="add-o"
      @click="addToken"
    />

    <div class="under">
      <div class="search">
        <div
          style="
          display: flex;
          background= white;
          margin: 0px 35px -17px 35px;
        "
        >
          <van-search
            v-model="value"
            shape="round"
            placeholder="请输入搜索任务关键词"
            @search="onSearch"
          />
        </div>
        <div style="display: flex; width: 71.5vw; transform: translateX(0.5vw)">
          <!-- 数据表 -->
          <el-table :data="tableData" style="width: 72vw" height="610px">
            <!-- Token名称表 宽150 -->
            <el-table-column label="名称" width="126">
              <template #default="scope">
                <div style="display: flex; align-items: center">
                  <el-icon><timer /></el-icon>
                  <span style="margin-left: 7px">{{ scope.row.name }}</span>
                </div>
              </template>
            </el-table-column>

            <!-- 账号信息表 宽260 -->
            <el-table-column label="账号信息" width="190">
              <template #default="scope">
                <el-popover
                  effect="light"
                  trigger="hover"
                  placement="top"
                  width="auto"
                >
                  <template #default>
                    <div>账号: {{ scope.row.username }}</div>
                    <div>密码: {{ scope.row.userPassword }}</div>
                  </template>
                  <template #reference>
                    <el-tag>{{ scope.row.username }}</el-tag>
                  </template>
                </el-popover>
              </template>
            </el-table-column>

            <!-- token值表 宽480 -->
            <el-table-column label="Token值" width="318">
              <template #default="scope">
                <el-popover
                  effect="light"
                  trigger="hover"
                  placement="top"
                  width="auto"
                >
                  <template #default>
                    <div>token: {{ scope.row.token }}</div>
                  </template>
                  <template #reference>
                    <!-- 做了超过50加...的操作 -->
                    <el-tag>{{ dataToken(scope.row.token) }}</el-tag>
                  </template>
                </el-popover>
              </template>
            </el-table-column>

            <!-- 有效时间表 宽210 -->
            <el-table-column label="有效时间" width="167">
              <template #default="scope">
                <el-popover
                  effect="light"
                  trigger="hover"
                  placement="top"
                  width="auto"
                >
                  <template #default>
                    <div>注册时间：{{ scope.row.updateTime }}</div>
                  </template>
                  <template #reference>
                    <el-tag
                      >距离过期还有：{{
                        formatDate(scope.row.updateTime)
                      }}</el-tag
                    >
                  </template>
                </el-popover>
              </template>
            </el-table-column>

            <!-- 操作方法表 宽300 方法handleEdit-->
            <el-table-column label="操作方法" width="268">
              <!-- 编辑操作按钮 -->
              <template #default="scope">
                <el-button
                  size="small"
                  @click="handleEdit(scope.$index, scope.row)"
                  >编辑</el-button
                >

                <!-- 查看操作按钮 方法showData-->
                <el-button
                  size="small"
                  type="primary"
                  @click="showData(scope.row)"
                  >查看</el-button
                >

                <!-- 删除操作按钮 方法deleteToken-->
                <el-button
                  size="small"
                  type="danger"
                  @click="deleteToken(scope.$index, scope.row)"
                  >删除</el-button
                >

                <!-- 刷新操作按钮 方法reNew-->
                <el-button size="small" type="success" @click="reNew(scope.row)"
                  >刷新</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </div>
        <div style="display: flex; margin-top: 3vh"></div>
      </div>

      <div class="bottom-component">
        <div style="text-align: center; transform: translateY(2.55vh)">
          <h2>
            欢迎大家来扩展
            <a href="https://github.com/Yanyutin753/PandoraNext-TokensTool"
              >pandoraNext-TokensTool v0.1.0
            </a>
            获取token
            <a href="https://chat.openai.com/api/auth/session">官网地址 </a>
            <a href="https://ai.fakeopen.com/auth">Pandora地址</a>
          </h2>
        </div>
        <br />
      </div>
    </div>
  </div>
  <!------------------------------------------------------------------------------------------------------>

  <!-- 修改token信息 主键 名称为show -->
  <van-dialog
    v-model:show="show"
    title="修改token信息"
    width="50vw"
    :close-on-click-overlay="true"
    :show-cancel-button="false"
    :show-confirm-button="false"
    class="requireTokenDialog"
  >
    <div style="display: block">
      <van-form @submit="RequireToken()">
        <van-cell-group inset>
          <br />
          <van-field
            v-model="temUsername"
            name="OpenAi用户名"
            label="OpenAi用户名"
            placeholder="OpenAi用户名"
            :rules="[{ required: true, message: '请填写OpenAi用户名' }]"
          />
          <br />
          <van-field
            v-model="temUserPassword"
            type="password"
            name="OpenAi密码"
            label="OpenAi密码"
            placeholder="OpenAi密码"
            :rules="[{ required: true, message: '请填写OpenAi密码' }]"
          />
          <br />
          <van-field name="switch" label="是否分享出来">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="temShared" />
            </template>
          </van-field>
          <br />
          <van-field name="switch" label="是否分享聊天记录">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="temShow_user_info" />
            </template>
          </van-field>
          <br />
          <van-field name="switch" label="是否显示金光">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="temPlus" />
            </template>
          </van-field>
          <br />
          <br />
          <van-field
            v-model="temPassword"
            type="temPassword"
            name="进入Token的密码"
            label="进入Token的密码"
            placeholder="进入Token的密码(选填)"
          />
          <br />
          <van-field
            v-model="temToken"
            rows="5"
            label="OpenAi的Token"
            type="textarea"
            maxlength="5000"
            placeholder="请填写OpenAi的Token(选填)"
            show-word-limit
          />
          <br />
        </van-cell-group>
        <div style="margin: 5.2px">
          <van-button round block color="#0ea27e" native-type="submit">
            提交
          </van-button>
        </div>
      </van-form>
    </div>
    <br />
  </van-dialog>
  <!------------------------------------------------------------------------------------------------------>

  <!-- 添加token信息 主键 名称为show_1 -->
  <van-dialog
    v-model:show="show_1"
    title="token信息"
    width="50vw"
    :close-on-click-overlay="true"
    :show-cancel-button="false"
    :show-confirm-button="false"
  >
    <div style="display: block">
      <van-form @submit="onAddToken()">
        <van-cell-group inset>
          <br />
          <van-field
            v-model="addName"
            name="Token用户名"
            label="Token用户名"
            placeholder="Token用户名"
            :rules="[{ required: true, message: '请填写Token用户名' }]"
          />
          <br />
          <van-field
            v-model="addUsername"
            name="OpenAi用户名"
            label="OpenAi用户名"
            placeholder="OpenAi用户名"
            :rules="[{ required: true, message: '请填写OpenAi用户名' }]"
          />
          <br />
          <van-field
            v-model="addUserPassword"
            type="temUserPassword"
            name="OpenAi密码"
            label="OpenAi密码"
            placeholder="OpenAi密码"
            :rules="[{ required: true, message: '请填写OpenAi密码' }]"
          />
          <br />
          <van-field name="switch" label="是否分享出来">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="addShared" />
            </template>
          </van-field>
          <br />
          <van-field name="switch" label="是否分享聊天记录">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="addShow_user_info" />
            </template>
          </van-field>
          <br />
          <van-field name="switch" label="是否显示金光">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="addPlus" />
            </template>
          </van-field>
          <br />
          <van-field
            v-model="addPassword"
            type="temPassword"
            name="进入Token的密码"
            label="进入Token的密码"
            placeholder="进入Token的密码"
          />
          <br />
        </van-cell-group>
        <div style="margin: 5.2px">
          <van-button round block color="#0ea27e" native-type="submit">
            提交
          </van-button>
        </div>
      </van-form>
    </div>
    <br />
  </van-dialog>
  <!------------------------------------------------------------------------------------------------------>

  <!-- 查看token信息 主键 名称为show_2 -->
  <van-dialog
    v-model:show="show_2"
    title="token信息"
    width="50vw"
    :close-on-click-overlay="true"
    :show-cancel-button="false"
    :show-confirm-button="false"
    class="showDialog"
  >
    <div style="display: block">
      <van-form>
        <van-cell-group inset>
          <br />
          <van-field
            v-model="temName"
            name="Token用户名"
            label="Token用户名"
            placeholder="Token用户名"
            :rules="[{ required: true, message: '请填写Token用户名' }]"
          />
          <br />
          <van-field
            v-model="temUsername"
            name="OpenAi用户名"
            label="OpenAi用户名"
            placeholder="OpenAi用户名"
            :rules="[{ required: true, message: '请填写OpenAi用户名' }]"
          />
          <br />
          <van-field
            v-model="temUserPassword"
            type="password"
            name="OpenAi密码"
            label="OpenAi密码"
            placeholder="OpenAi密码"
            :rules="[{ required: true, message: '请填写OpenAi密码' }]"
          />
          <br />
          <van-field name="temShared" label="是否分享出来">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="temShared" />
            </template>
          </van-field>
          <br />
          <van-field name="temShow_user_info" label="是否分享聊天记录">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="temShow_user_info" />
            </template>
          </van-field>
          <br />
          <van-field name="temPlus" label="是否显示金光">
            <template #right-icon>
              <van-switch active-color="#0ea27e" v-model="temPlus" />
            </template>
          </van-field>
          <br />
          <van-field
            v-model="temPassword"
            type="temPassword"
            name="进入Token的密码"
            label="进入Token的密码"
            placeholder="进入Token的密码"
          />
          <br />
          <van-field
            v-model="temToken"
            rows="5"
            label="OpenAi的Token"
            type="textarea"
            maxlength="5000"
            placeholder="请填写OpenAi的Token"
            show-word-limit
          />
          <br />
        </van-cell-group>
        <br />
      </van-form>
    </div>
  </van-dialog>
  <!------------------------------------------------------------------------------------------------------>
</template>

<script lang="ts" setup>
// 导入类
import { Timer } from "@element-plus/icons-vue";
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import png from "../asserts/chatGpt.jpg";
import { ElMessage, ElMessageBox } from "element-plus";

/**
 *   <!-- 添加token信息 主键 名称为show_1 -->
 *   <!-- 添加token信息 主键 名称为show_1 -->
 *   <!-- 查看token信息 主键 名称为show_2 -->
 *
 */
const show = ref(false);
const show_1 = ref(false);
const show_2 = ref(false);

//页头图片 image
const image = png;

/**
 * router 切换页面
 */
const router = useRouter();

/**
 * 定义User类接口
 */
interface User {
  name: string;
  token: string;
  username: string;
  userPassword: string;
  shared: boolean;
  show_user_info: boolean;
  plus: boolean;
  password: string;
  updateTime: string;
}

/**
 * 查看或者修改token信息参数
 */
const temName = ref("");
const temToken = ref("");
const temUsername = ref("");
const temUserPassword = ref("");
const temShared = ref(false);
const temShow_user_info = ref(false);
const temPlus = ref(false);
const temPassword = ref();
const tableData = ref<User[]>([]);

/**
 * 添加用户信息参数
 */
const addName = ref("");
const addUsername = ref("");
const addUserPassword = ref("");
const addShared = ref(false);
const addShow_user_info = ref(false);
const addPlus = ref(false);
const addPassword = ref();

//中间变量，用于删除数据
let name = "";

/**
 * 控制悬浮球位置
 * 单位%
 */

var y = window.innerHeight * 0.11;
var x = window.innerWidth * 0.842;

const iconSize = ref(window.innerHeight * 0.1);
console.log(window.innerHeight.toString());
const offset_task = ref({ x: x, y: y });

//搜索值
const value = ref<string>("");

/**
 * 用jwt令牌验证身份
 * 未通过者返回到/login
 */
const token = localStorage.getItem("jwtToken"); // 从localStorage获取JWT令牌
if (!token) {
  router.replace("/login");
}

const headers = {
  Authorization: `Bearer ${token}`,
};
/**
 * 用jwt令牌验证身份函数
 */
const fetchLoginToken = () => {
  axios
    .post("/api/loginToken?token=" + token)
    .then((response) => {
      if (response.data.code == 0) {
        console.error(response.data.data);
        router.replace("/login");
        return;
      }
      // 从解码后的令牌中获取特定的数据
      console.log(response.data.data);
      // 在这里处理登录令牌接口的响应
      // 如果需要执行一些特定的操作，可以在这里添加代码
    })
    .catch((error) => {
      console.error("请求loginToken接口失败", error);
      router.replace("/login");
    });
};

const onSearch = (value: string) => {
  fetchDataAndFillForm(value);
};
/**
 * 获取数据操作，并把数据返回到tableData
 * 用于展示
 */
const fetchDataAndFillForm = async (value: string) => {
  try {
    const response = await axios.get(
      `/api/seleteToken?name=${value}`,
      {
        headers,
      }
    );
    const data = response.data.data;
    console.log(data);

    // 如果服务器返回的数据是一个数组，你可以遍历数据并将每个对象转化为User类型
    if (Array.isArray(data)) {
      const resUsers: User[] = data.map((item: User) => ({
        name: item.name,
        username: item.username,
        userPassword: item.userPassword,
        token: item.token,
        shared: item.shared,
        show_user_info: item.show_user_info,
        password: item.password,
        plus: item.plus,
        updateTime: item.updateTime,
      }));

      // 将用户数据添加到tableData
      tableData.value = resUsers;
    }
  } catch (error) {
    console.error("获取数据失败", error);
    ElMessage("获取数据失败");
  }
};

// 在组件加载完成后自动触发数据加载和填充
onMounted(() => {
  fetchLoginToken();
  onSearch(value.value);
});

/**
 * 用于用户信息设置
 */
const activeIndex = ref("-1");
const handleSelect = (key: string, keyPath: string[]) => {
  console.log(key, keyPath);
};
const handleEdit = (index: number, row: User) => {
  console.log(index, row);
  temName.value = row.name;
  console.log(temName.value);
  temUsername.value = row.username;
  temUserPassword.value = row.userPassword;
  temToken.value = row.token;
  temShared.value = row.shared;
  temShow_user_info.value = row.show_user_info;
  temPlus.value = row.plus;
  temPassword.value = row.password;
  show.value = true;
};

/**
 * 添加token开启函数
 * 类user
 */
const addToken = () => {
  show_1.value = true;
};

/**
 * 添加token函数
 * 类user
 */
const onAddToken = () => {
  const api = {
    name: addName.value,
    username: addUsername.value,
    userPassword: addUserPassword.value,
    shared: addShared.value,
    show_user_info: addShow_user_info.value,
    plus: addPlus.value,
    password: addPassword.value,
    updateTime: new Date().toString(),
  };

  fetch("/api/addToken", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${token}`,
    },
    body: JSON.stringify(api),
  })
    .then((response) => response.json()) // 将 .json() 放在正确的位置
    .then((data) => {
      if (data.code == 1) {
        console.log(data.data);
        api.token = data.data;
        tableData.value.unshift(api);
        ElMessage("添加成功！");
      } else {
        ElMessage(data.msg);
      }
    })
    .catch((error) => {
      console.error("请求addToken接口失败", error);
      ElMessage("添加失败！");
      // 处理请求失败的情况
    });
  temName.value = "";
  temUsername.value = "";
  temUserPassword.value = "";
  temToken.value = "";
  show_1.value = false;
};

/**
 * 展示token函数
 * 类user
 */
const showData = (row: User) => {
  temName.value = row.name;
  temUsername.value = row.username;
  temUserPassword.value = row.userPassword;
  temToken.value = row.token;
  temShared.value = row.shared;
  temShow_user_info.value = row.show_user_info;
  temPlus.value = row.plus;
  temPassword.value = row.password;
  show_2.value = true;
};

/**
 * 修改token函数
 * 类user
 */
const RequireToken = () => {
  const api = {
    name: temName.value,
    token: temToken.value,
    username: temUsername.value,
    userPassword: temUserPassword.value,
    shared: temShared.value,
    show_user_info: temShow_user_info.value,
    plus: temPlus.value,
    password: temPassword.value,
  };
  fetch("/api/requiredToken", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${token}`,
    },
    body: JSON.stringify(api),
  })
    // 将 .json() 放在正确的位置
    .then((response) => response.json())
    .then((data) => {
      if (data.code == 1) {
        console.log(data.data);
        ElMessage(data.data);
        console.log(tableData.value);
        for (let i = 0; i < tableData.value.length; i++) {
          console.log(tableData.value[i].name);
          if (tableData.value[i].name === temName.value) {
            tableData.value[i].token = api.token;
            tableData.value[i].username = api.username;
            tableData.value[i].userPassword = api.password;
            tableData.value[i].shared = api.shared;
            tableData.value[i].show_user_info = api.show_user_info;
            tableData.value[i].plus = api.plus;
            tableData.value[i].password = api.password;
            show.value = false;
            break; // 找到匹配的元素后跳出循环
          }
        }
      } else ElMessage(data.msg);
    })
    .catch((error) => {
      console.error("请求requireToken接口失败", error);
      ElMessage("修改失败！");
    });
};

/**
 * 重启pandora函数
 */
const AgainPandora = async () => {
  const response = await axios.get(`/api/restart`, {
    headers,
  });
  const data = response.data.data;
  console.log(data);
  if (data != null && data != "") {
    ElMessageBox.alert(data, "温馨提醒", {
      confirmButtonText: "OK",
      callback: () => {
        ElMessage({
          type: "info",
          message: "感谢Pandora大佬！",
        });
      },
    });
  } else ElMessage(response.data.msg);
};

/**
 * 刷新Token函数
 */
const reNew = (row: User) => {
  console.log(row);
  console.log(row.token);
  const api = {
    name: row.name,
    token: row.token,
    username: row.username,
    userPassword: row.userPassword,
    shared: row.shared,
    show_user_info: row.show_user_info,
    plus: row.plus,
    password: row.password,
  };

  fetch("/api/updateToken", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${token}`, // 确保 token 变量已定义
    },
    body: JSON.stringify(api),
  })
    .then((response) => {
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      return response.json();
    })
    .then((data) => {
      if (data != null && data != "") {
        row.token = data.data;

        const now: Date = new Date();
        const formattedTime = `${now.getFullYear()}-${(now.getMonth() + 1)
          .toString()
          .padStart(2, "0")}-${now.getDate().toString().padStart(2, "0")} ${now
          .getHours()
          .toString()
          .padStart(2, "0")}:${now
          .getMinutes()
          .toString()
          .padStart(2, "0")}:${now.getSeconds().toString().padStart(2, "0")}`;

        row.updateTime = formattedTime;
        ElMessageBox.alert("更新成功!", "温馨提醒", {
          confirmButtonText: "OK",
          callback: () => {
            ElMessage({
              type: "info",
              message: "感谢Pandora大佬！",
            });
          },
        });
      } else {
        ElMessage(data.msg);
      }
    })
    .catch((error) => {
      console.error("Error:", error);
    });
};

/**
 * 删除Token函数
 * 参数 user
 */
const deleteToken = (index: number, row: User) => {
  let msg = "";
  ElMessageBox.confirm(
    "您确定要删除这个Token吗，删除之后就找不到咯，请您要仔细认真考虑哦！",
    "温馨提示",
    {
      confirmButtonText: "确定",
      cancelButtonText: "取消",
      type: "warning",
    }
  )
    .then(() => {
      axios
        .put(`/api/deleteToken?name=${row.name}`, null, {
          headers,
        })
        .then((response) => {
          msg = "删除成功！";
          // 从数组中移除商品项
          console.log(response.data.data);
          tableData.value.splice(index, 1);
          ElMessage({
            type: "success",
            message: msg,
          });
        })
        .catch((error) => {
          // 处理完成失败的逻辑
          console.error("删除失败", error);
        });
    })
    .catch(() => {
      ElMessage({
        type: "info",
        message: "删除取消！",
      });
    });
};

/**
 * 获取token的过期时间
 */
const formatDate = (value) => {
  if (!value) return "";

  // 当前时间
  var nowTime = new Date().getTime();

  // 给定的时间
  var givenTime = new Date(value).getTime();

  // 计算两个时间之间的差距
  let timeDiff = Math.ceil((nowTime - givenTime) / (1000 * 60 * 60 * 24));

  return timeDiff >= 10
    ? "已经过去了至少10天"
    : Math.ceil(10 - timeDiff) + "天";
};

/**
 * 更改Token显示操作
 */
const dataToken = (value) => {
  return value.substring(0, 40) + "...";
};

/**
 * 退出登录操作
 */
const logout = () => {
  // 删除本地存储的 token
  localStorage.removeItem("jwtToken");
  // 使用 Vue Router 进行页面跳转到登录页面
  router.replace("/login");
};
</script>

<style>
.van-floating-bubble {
  width: 6vh;
  height: 6vh;
  background: #0ea27e;
}
.van-floating-bubble__icon {
  font-size: 50px;
}
.content {
  flex: 1; /* 占据剩余空间 */
  display: flex;
  background: rgb(184 255 225 / 22%);
  zoom: 1;
  /* 禁止页面内容缩放 */
  width: 100vw;
  /* 设置容器宽度 */
  height: 100vh;
  /* 设置容器高度，使其占满整个视口 */
  overflow-y: auto;
  /* 显示垂直滚动条 */
  overflow-x: hidden;
  /* 隐藏水平滚动条 */
  flex-direction: column;
}
.van-dialog {
  top: 55vh;
  height: auto;
}
.van-field__label {
  width: 100px;
}
.el-table .cell {
  font-size: 14px;
  line-height: 45px;
}
.el-tag {
  font-size: 12.6px;
}
.el-button--small {
  font-size: 12.6px;
}
.el-page-header__title {
  transform: translate(-42.7px, -7px);
  font-size: 27.3px;
  font-weight: 500;
}
.el-page-header__content {
  transform: translate(3vw, -3.5vh);
  font-size: 37px;
  color: var(--el-text-color-primary);
}

.el-avatar--circle {
  transform: translate(-14px, 2.8vh);
  border-radius: 50%;
}

/* 集合列字体大小 */
.el-menu--horizontal > .el-sub-menu .el-sub-menu__title {
  font-size: 14px;
}

/* 集合内容字体大小 */
.el-menu-item {
  font-size: 12.6px;
}

.van-dialog__header {
  font-size: 14px;
}

.requireDialog {
  height: auto;
}

.requireTokenDialog {
  height: auto;
}
.addTokenDialog {
  height: auto;
}
.showDialog {
  height: auto;
}
.van-field__label {
  font-size: 10.6px;
}

/* 集合内框内字体颜色 */
.el-tag {
  --el-tag-text-color: #0ea27e;
  background-color: #f4fffd;
}

/* 集合内各数据位置 */
.el-table__inner-wrapper {
  margin-left: 50px;
  margin-bottom: 7px;
  overflow: auto;
}

/* 字体 a 超链接 h2 字体大小 */
a {
  color: #0ea27e;
}

h2 {
  font-size: 1.75vh;
  color: #606266;
  margin: 0px;
}

/* 表格上下间距 */
.el-table--large .el-table__cell {
  padding: 17.5px 0;
}

.el-menu--horizontal.el-menu {
  border-bottom: 1px solid #fff;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  border-radius: 10%;
}
/* 菜单左右方法 */
.flex-grow {
  flex-grow: 1;
}
/* 搜索框 */
.van-search {
  width: 100%;
  margin: 14px;
}

.search {
  box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.2);
  transform: translate(14vw, 3.5vh);
  width: 72vw;
  background: white;
  border-radius: 10px;
}

.bottom-component {
  /* 样式设置，具体根据你的需求进行调整 */
  flex: 1; /* 占据剩余空间 */
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: auto;
}

.under {
  flex: 1; /* 占据剩余空间 */
  display: flex;
  flex-direction: column;
}

.el-table {
  width: 95%;
  max-width: 100%;
}
</style>