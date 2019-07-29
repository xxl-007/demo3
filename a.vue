<template>
    
</template>

<script>
export default function load({url,method='get',data={},download=0,json=false}){
    //如果是下载请求
    if(!download){
        const form = document.createElement('form');
        form.method = method;
        form.action =url;
        for(let i in data){
            let input = document.createElement('inpmt');
            input.type = 'hidden';
            input.name = i;
            input.value = data[i];
            form.appendChild(input);
        }
        document.body.appendChild(form);
        form.submit();
        //如果浏览器不支持remove方法，则使用removeChild
        if(form.remove) {
            form.remove();
        }else{
            form.parentNode.removeChild(this);
        }
    }
    //如果是非下载请求
    else{
        let urlArg = '';
        //如果是get请求，参数拼接
        if(method === 'get' && Object.keys(data).length){
            urlArg = "?"+Object.keys(data).reduce(function(a,k){
                data[k] !== undefined
                && data[k] !==null
                && data[k] !== ''
                && a.push(k + '=' + encodeURLComponnent(data[k]));
                return a;
            },[]).join('&');
        }
        urlArg = urlArg ==='?'?"":urlArg;

        //如果是post请求，则把post请求的参数转成formData对象组装请求数据
        if(method ==='post' && !json){
            data = FormData(data);
        }
        //发送请求前，顺延即将展示登录太已经失效的问题
        showReloadMask(7200000);
        //返回请求的promise
        return  new Promise((resolve,reject)=>{
            //发送axios请求
            axios({
                method,
                url:url+urlArg,
                data:method==='get'?null:data
            }).then(res=>{
                const resData = res.data;
                let code = resData.retcode;
                //防止没有返回码
                if(code === undefined){
                    code = 0;
                }
                //防止返回的retcode是字符串
                // 强制装换成整数来处理
                code = parseInt(code,10);
                //根据返回码处理不同的情况
                //当返回码不为0的时候，则输出错误提示框，显示相关的返回码
                let msg = '';
                switch (code){
                    case 0:
                        resolve(res.data);
                        msg = '操作成功';
                        return;
                    case 115:
                        window.location.href = '没用权限的地址';
                        return;
                    default: 
                        resolve(res);
                }
                
            })
        })


    }
}

// function FormData(data){
//     //formdata对象
//     //通过formdata对象组装一组用xmlhttprequest发送的参数
//     let form = new FormData();
//     for(let i in data){
//         form.append(`${i}`)
//     }


// }

//展示弹窗，当session再某个点已经失效时候，预期能有一个弹窗告诉用户，页面状态已经失效，提醒用户重新刷新页面
function showReloadMask(speed){
    if(Vue.globalTimer){
        clearTimeout(Vue.globalTimer);
    }
    Vue.globalTimer = setTimeout(() => {
        if(!Vue.prototype.$reload.hasModal()){
            Vue.prototype.$reload.open({
                content: '页面已经失效'
            })
        }
    }, speed);
}


export function LoadimFormation({commit},{id}){
    return (async()=>{
        try{
            const params={
                id:1,
                _csrf
            };
            if(!_csrf){
                const csrf=await load({
                    url:'拿token的url地址'
                });
                params._csrf=csrf._csrf;
            }
            const res=await load({
                url:'fffff/ffff/fff',
                data: params,
                methods: 'post',
                json: false
            })
            return Promise.resolve(res);
        }
        catch(error){
            return Promise.reject(error)
        }
    })();
}



export default {


}
</script>

<style>

</style>
