# 欢迎来到我的Github页面。

本页面可能将会用于发布我的项目:)

##  测试内容
摘抄一段代码上去算了。
```
package com.example.posttest;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;

import android.annotation.SuppressLint;
import android.content.Intent;
import android.graphics.Color;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.os.Parcelable;
import android.text.Spannable;
import android.text.SpannableStringBuilder;
import android.text.method.LinkMovementMethod;
import android.text.style.ClickableSpan;
import android.text.style.ForegroundColorSpan;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.URL;
import java.util.HashMap;

import javax.net.ssl.HttpsURLConnection;

public class MainActivity extends AppCompatActivity {
    //=====================
    private TextView tvNoAccount;
    private EditText usernameEt;
    private EditText passwordEt;
    private Intent intent = new Intent();
    private final SpannableStringBuilder style = new SpannableStringBuilder();
    private MHandler mHandler = new MHandler();
    private Message message = new Message();
    private String jsonData = new String();
    private JSONObject jsonObject = new JSONObject();

    //==========重写函数==========
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        bindEditTexts();
        setRegPageEntrance();
        listenToButton();
    }

    //==========自定义函数============

    private void bindEditTexts(){
        usernameEt = findViewById(R.id.editText2);
        passwordEt = findViewById(R.id.editText3);
        passwordEt.setInputType(129);
    }
    //注册页面入口方法
    private void setRegPageEntrance(){
                style.append("没有账号？立刻注册！");
        tvNoAccount = findViewById(R.id.textView);
        ClickableSpan clickableSpan = new ClickableSpan() {
            @Override
            public void onClick(@NonNull View widget) {
                intent = new Intent(MainActivity.this,
                        RegisterActivity.class);
                startActivity(intent);
            }
        };
        style.setSpan(clickableSpan, 5, 10, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
        tvNoAccount.setText(style);
        ForegroundColorSpan foregroundColorSpan = new ForegroundColorSpan(Color.parseColor("#0000FF"));
        style.setSpan(foregroundColorSpan, 5, 10, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
        tvNoAccount.setMovementMethod(LinkMovementMethod.getInstance());
    }
    //监听按钮方法
    private void listenToButton(){
        Button buttonToLogIn = findViewById(R.id.login_button);
        buttonToLogIn.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View v) {
                HashMap<String,String> mMap = new HashMap<>();

                mMap.put("username",usernameEt.getText().toString());
                mMap.put("password",passwordEt.getText().toString());

                loginRequest("https://www.wanandroid.com/user/login",mMap);
            }
        });
    }

    //请求登录方法
    private void loginRequest(String mUrl, HashMap<String, String> params){
        new Thread(
                ()->{
                    try{
                        Message messageChildThread = new Message();
                        URL url = new URL(mUrl);
                        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
                        connection.setRequestMethod("POST");
                        connection.setConnectTimeout(8000);
                        connection.setReadTimeout(8000);
                        connection.setDoInput(true);
                        connection.setDoOutput(true);
                        StringBuilder dataToWrite = new StringBuilder();
                        for (String key : params.keySet()) {
                            dataToWrite.append(key).append("=").append(params.get(key)).append("&");
                        }
                        connection.connect();
                        OutputStream outputStream = connection.getOutputStream();
                        outputStream.write(dataToWrite.substring(0,dataToWrite.length()-1).getBytes());
                        InputStream in = connection.getInputStream();

                        String responseData = streamToString(in);
                        messageChildThread.obj = responseData;
                        mHandler.sendMessage(messageChildThread);
                    }catch(Exception e){
                        e.printStackTrace();
                    }
                }
        ).start();
    }

    //将stream转化为string的方法
    String streamToString(InputStream in){
        StringBuilder sb = new StringBuilder();
        String oneLine;
        BufferedReader reader = new BufferedReader(new InputStreamReader(in));
        try {
            while((oneLine = reader.readLine())!=null){
                sb.append(oneLine).append('\n');
            }

        }catch(Exception e){
            e.printStackTrace();
        }
        finally {
            try{
                in.close();
            }catch (Exception e){
                e.printStackTrace();
            }

        }
        return sb.toString();
    }

    //查看登陆失败状态
    private int logErrCode(){
        int judgement;
        try {
            judgement = jsonObject.getInt("errorCode");
        } catch (JSONException e) {
            judgement = -999;
            e.printStackTrace();
        }
        return judgement;
    }

    //查看登陆失败信息
    private String logErrMsg(){
        String msg ;
        try{
            msg = jsonObject.getString("errorMsg");
        } catch (Exception e) {
            msg = "Unknown error";
            e.printStackTrace();
        }
        return msg;
    }

    private void showLoginState(){

    }

    //请求完成并得到数据之后的方法
    private void doAfterRequestComplete(){
        int errCode = logErrCode();
        String errMsg = logErrMsg();

        if(errCode != 0){
            Toast.makeText(this,"登陆失败\n错误码："+errCode+"\n错误信息："+errMsg,Toast.LENGTH_LONG).show();
            return;
        }
        else{
            Toast.makeText(this,"登陆成功！",Toast.LENGTH_SHORT).show();
            intent = new Intent(MainActivity.this,LoggedInActivity.class);
            intent.putExtra("jsonData",jsonData);
            startActivity(intent);
        }
    }

    //==========内部类===========

    @SuppressLint("HandlerLeak")
    private class MHandler extends Handler {
        @Override
        public void handleMessage(@NonNull Message msg) {
            super.handleMessage(msg);

            jsonData = (String)msg.obj;
            try{
                message = msg;
                jsonData = (String) message.obj;
                jsonObject = new JSONObject(jsonData);
                doAfterRequestComplete();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

###  如何联系？

邮箱：shuhuanx@yeah.net

### 关于本页
本页内发布原创内容版权（如果有）均为本人所属，任何人在未经许可的情况下擅自使用将有可能承担法律责任。
本页的域名https://imaginarycrisis.xyz/ 以及 https://www.imaginarycrisis.xyz/ 均为本人所有。
