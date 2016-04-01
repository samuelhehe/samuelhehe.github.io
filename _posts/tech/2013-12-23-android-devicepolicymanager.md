---
layout: post
title: Android DevicePolicyManager 使用
category: 技术
tags: [DevicePolicyManager ]
keywords:  Android DevicePolicyManager
description: Android DevicePolicyManager 使用
---

Android DevicePolicyManager 使用
====================================

>DevicePolicyManager是个公司做的EMM很大一部分就是围绕这个类展开的。记得有个很强大的病毒就是利用了DevicePolicyManager 与激活设备绑定之后，用户无法卸载该用户，成千上万的设备被感染，个人信息被获取，清除数据等等邪恶的操作。

凡事都有正反两面，关键是看怎么使用了。接下来要记录一下关于这个非常厉害类的使用方法，设备管理器的激活，防止用户卸载，以及扩展使用。

个人觉得英文不好，略识几个单词，但是喜欢看官方文档。大家不妨先看一下官方API的描述，接下来我会详细介绍：[http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html](http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html)

首先需要配置一下 使用设备管理器的哪些功能

```
<?xml version="1.0" encoding="utf-8"?>
<device-admin
	xmlns:android="http://schemas.android.com/apk/res/android">
	<uses-policies>
		<limit-password />
		<!-- 限制密码规则的限制 -->
		<watch-login />
		<!-- 监听登录 -->
		<reset-password />
		<!-- 重置密码 -->
		<force-lock />
		<!-- 强制锁屏 -->
		<wipe-data />
		<!-- 恢复出厂设置 清除数据 -->
	</uses-policies>
</device-admin>
```

配置一下关于设备管理员的监听receiver
```

<receiver
	android:name="com.xxx.emm.receiver.DeviceAdminSampleReceiver"
	android:permission="android.permission.BIND_DEVICE_ADMIN">
	<meta-data
		android:name="android.app.device_admin"
		android:resource="@xml/device_admin" />
 <intent-filter>
	<action android:name="android.app.action.DEVICE_ADMIN_ENABLED" />
	</intent-filter>
</receiver>
```

这个工具的使用当然需要用户的同意，也就是激活。 看一下激活组件：

```
private ComponentName emmmDeviceAdmin 	emmmDeviceAdmin = new ComponentName(this,DeviceAdminSampleReceiver.class);
	        private DevicePolicyManager devicePolicyManager = (DevicePolicyManager) getSystemService(Context.DEVICE_POLICY_SERVICE);    

       /**
	 * Helper to determine if we are an active admin
	 */
	private boolean isActiveAdmin() {
		return devicePolicyManager.isAdminActive(emmmDeviceAdmin);
	} 
       /**
	 * 启动系统激活设备管理器页面
	 */
	protected void launchDeviceAdmin() {
		Intent intent = new Intent(DevicePolicyManager.ACTION_ADD_DEVICE_ADMIN);
		intent.putExtra(DevicePolicyManager.EXTRA_DEVICE_ADMIN, emmmDeviceAdmin);
		intent.putExtra(DevicePolicyManager.EXTRA_ADD_EXPLANATION, getString(R.string.add_admin_extra_app_text)); /// 这句话告诉用户激活这个组件的作用以及其他内容
		startActivityForResult(intent, REQUEST_CODE_ENABLE_ADMIN);
	}
```
接下来看一下监听类的实现，以及各个方法的作用：
  
```
/**
 * 
 * Receiver The callback methods, like the base
 * BroadcastReceiver.onReceive() method, happen on the main thread of the
 * process. Thus long running operations must be done on another thread. Note
 * that because a receiver is done once returning from its receive function,
 * such long-running operations should probably be done in a Service.
 */
public class DeviceAdminSampleReceiver extends DeviceAdminReceiver {

	private static final String TAG = "DeviceAdminSampleReceiver";
	public static int ERR_TIME = 0;
	public static int ERR_TIME_TOTAL = 0;

	void showToast(Context context, String msg) {
		String status = context.getString(R.string.admin_receiver_status, msg);
		Toast.makeText(context, status, Toast.LENGTH_LONG).show();
	}

	@Override
	public void onEnabled(Context context, Intent intent) {
		// showToast(context,
		// context.getString(R.string.admin_receiver_status_enabled));
		showToast(context, "当前应用已与设备管理器绑定!");
	}

	@Override
	public synchronized CharSequence onDisableRequested(Context context, Intent intent) {

		return context.getString(R.string.admin_receiver_status_disable_warning); /// 告诉用户即将取消激活该组件。
	}

	@Override
	public void onDisabled(Context context, Intent intent) {
		showToast(context,context.getString(R.string.admin_receiver_status_disabled));
	}

	@Override
	public void onPasswordChanged(Context context, Intent intent) {
		showToast(context,
				context.getString(R.string.admin_receiver_status_pw_changed));
	}

	@Override
	public void onPasswordFailed(Context context, Intent intent) {
		ERR_TIME_TOTAL += 1;
		if (ERR_TIME_TOTAL >= ERR_TIME) {
			showToast(context, "密码输入错误" + ERR_TIME_TOTAL + "次,清除系统资料!");
			System.out.println("密码错误已达:" + ERR_TIME_TOTAL);
		} else {
			showToast(context, "密码输入错误" + ERR_TIME_TOTAL + "次，连续错误" + ERR_TIME
					+ "次将清除系统资料!");
		}
		// showToast(context,
		// context.getString(R.string.admin_receiver_status_pw_failed));
	}

	@Override
	public void onPasswordSucceeded(Context context, Intent intent) {
		// showToast(context,
		// context.getString(R.string.admin_receiver_status_pw_succeeded));
		showToast(context, "密码输入成功!");
		ERR_TIME_TOTAL = 0;
	}

	@Override
	public void onPasswordExpiring(Context context, Intent intent) {
		DevicePolicyManager dpm = (DevicePolicyManager) context
				.getSystemService(Context.DEVICE_POLICY_SERVICE);
		long expr = dpm.getPasswordExpiration(new ComponentName(context,
				DeviceAdminSampleReceiver.class));
		long delta = expr - System.currentTimeMillis();
		boolean expired = delta < 0L;
		String message = context
				.getString(expired ? R.string.expiration_status_past: R.string.expiration_status_future);
		showToast(context, message);
		Log.v(TAG, message);
	}

  // 设置用户密码的使用规则
    public void password_quality_setting(View v) {
        // PASSWORD_QUALITY_ALPHABETIC
        // 用户输入的密码必须要有字母（或者其他字符）。
        // PASSWORD_QUALITY_ALPHANUMERIC
        // 用户输入的密码必须要有字母和数字。
        // PASSWORD_QUALITY_NUMERIC
        // 用户输入的密码必须要有数字
        // PASSWORD_QUALITY_SOMETHING
        // 由设计人员决定的。
        // PASSWORD_QUALITY_UNSPECIFIED
        // 对密码没有要求。
        Intent intent = new Intent(DevicePolicyManager.ACTION_SET_NEW_PASSWORD); 
        devicePolicyManager.setPasswordQuality(componentName, DevicePolicyManager.PASSWORD_QUALITY_NUMERIC);  //// 数字型密码。
        startActivity(intent);
    }

}
```

接下来通过设备管理员可以实现一些关于相机，锁屏，清除数据，恢复出厂设置，等等功能的实现：

```
	/**
	 * 锁设备
	 * 
	 * @param pwd  密码
	 *           
	 */
	public void lockDevice(String pwd) {

		L.d(this.getClass(), "password : " + pwd);
		if (pwd.equals("")) { // 無密碼直接關螢幕
			mDPM.lockNow();
		} else {
			mDPM.setPasswordQuality(mDeviceAdminSample,
					DevicePolicyManager.PASSWORD_QUALITY_UNSPECIFIED); // 修改密碼前需先將密碼複雜程度設為"Unspecified"後，再將密碼設為""包住之空字串，才能設定成功。
			mDPM.resetPassword(pwd,
					DevicePolicyManager.RESET_PASSWORD_REQUIRE_ENTRY);
			mDPM.lockNow();
		}
	}
	/**
	 * 清除设备数据.
	 */
	public void removeWipe() {
		L.i(this.getClass(), "wraning remove wipe ");

		mDPM.wipeData(0); // Wipe internal data, like a factory reset.
		mDPM.wipeData(DevicePolicyManager.WIPE_EXTERNAL_STORAGE);
	}
	/**
	 * 移除设备密码. 设备解锁.
	 */
	public void unLockDevice() {
		mDPM.setPasswordMinimumLength(mDeviceAdminSample, 0);
		mDPM.setPasswordQuality(mDeviceAdminSample,
				DevicePolicyManager.PASSWORD_QUALITY_UNSPECIFIED); // 移除密碼前需先將密碼複雜程度設為"Unspecified"後，再將密碼設為""包住之空字串，才能設定成功。
		mDPM.resetPassword("", DevicePolicyManager.RESET_PASSWORD_REQUIRE_ENTRY); // 移除密碼
	}

       /**
	 * 相机可用
	 */
	public void enableCamera() {

		mDPM.setCameraDisabled(mDeviceAdminSample, false);
		L.i(this.getClass(), "camera is enabled ...");
	}
    /**
      * 设置5秒后锁屏
      */  
       public void btnlocktime(View v) {
           mDPM.setMaximumTimeToLock(componentName, 5000);
       }

```

### 总结： 
DevicePolicyManager 的功能很多，如果实际开发中用到的就可以在配置文件中配置，不用则无需配置，激活选项过多容易引起用户的反感。在原生系统中测试，该组件被激活后，用户无法卸载，除非取消激活设备管理器，与此同时这就给了黑客了一些灵感， 可以在取消激活的方法中写一些代码，导致用户无法取消激活，也就会导致用户卸载不掉该应用。但是在某一些系统中激活后可以卸载，比如说MIUI
