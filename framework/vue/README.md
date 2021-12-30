# vue3的例子



## 遍历内容到html中



```html 

            <div class="row">
                <div class="col-sm-4 mb-3">
                    <label for="state">物理类型</label>
                    <select class="form-select w-100 mb-0" id="state" name="state" aria-label="State select example" onchange="get_sub_attr(this.value, 'tmodel')">

                        <option  selected="0">请选择</option>
                        <option v-for="pht in attr.physical_type" :value="pht.id">@{ pht.text }</option>
                    </select>
                </div>
                <div class="col-sm-4 mb-3">
                  <label for="state">分类</label>
                  <select class="form-select w-100 mb-0" id="state" name="state" aria-label="State select example" onchange="get_sub_attr(this.value, 'specs')">
                      <option  selected="" value="0">请选择</option>
                        <template v-for="dt in device_type" >
                          <option  :value="dt.id">@{ dt.text }</option>
                        </template>
                  </select>
                </div>
            </div>
```





## 用axios请求服务端，并修改vue实例中的data



```javascript
const AttributeVM = Vue.createApp({
  delimiters: ['@{','}'],
    data() {
      return {
        attr: {},
        pool_city: [],
        device_type: [],
        specs: []
      }
    },
    created: function() {
        axios
          .get('/c_resource/get-attr/def/0/0/')
          .then(response => {
            this.attr = response.data;
          })
          .catch(error => {
            console.log(error);
          })
      
    },
    mounted: function () {
      console.log(this.attr);
    }
  }).mount('#attribute');

var pool_id = 0;
function get_sub_attr(code, level){
  axios
    .get('/c_resource/get-attr/' + level + '/'+ code +'/'+ pool_id +'/')
    .then(response => {
      if(level == 'area'){
        pool_id = code;
        AttributeVM.$data.pool_city = response.data.pool_city;
      }else if (level == 'tmodel'){
        console.log(response.data.device_type);
        AttributeVM.$data.device_type = response.data.device_type;
      }else if (level == 'specs'){
        console.log(response.data.specs);
        AttributeVM.$data.specs = response.data.specs;
      }
    })
    .catch(error => {
      console.log(error);
    });
}
```





