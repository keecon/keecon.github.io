---
layout: post
title:  "Jenkins xcworkspace 빌드하기"
date:   2019-11-05 00:00:00
author: hwlee
categories: Dev
tags:	유니티
cover:  "/assets/logo_keecon_orange.png"
---

젠킨스에서 유니티프로젝트 admob을 빌드하게 되면 종속성 문제가 발생하나 CocoaPods을 사용하여 자동으로 해결할 수 있다.
[https://developers.google.com/admob/unity/start]

## xcodeproj -> xcworkspace로 변경
1. cocoaPods 설치
커맨드라인에서 sudo gem install cocoapods 로 설치

2. 젠킨스 해당 프로젝트 구성에서 xcode 설정 위에 Execute shell을 추가하고 다음을 입력
/usr/local/bin/pod install --project-directory=경로
![Alt text](/assets/unity_jenkins_xcworkspace.png)

3. Advanced Xcode build options 수정
![Alt text](/assets/unity_jenkins_xcworkspace_1.png)

## 참고사이트
[https://devmae.tistory.com/424]

## bitcode 문제 해결
bitcode 정보[https://ehdrjsdlzzzz.github.io/2019/03/10/Bitcode-in-iOS/]
필요없다면 꺼버리자.

{% highlight sh %}
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;
using UnityEditor.Callbacks;
using UnityEditor.iOS.Xcode;

public static class MyBuildPostprocess
{
    [PostProcessBuild(999)]
    public static void OnPostProcessBuild( BuildTarget buildTarget, string path)
    {
        if(buildTarget == BuildTarget.iOS)
        {
            string projectPath = path + "/Unity-iPhone.xcodeproj/project.pbxproj";

            PBXProject pbxProject = new PBXProject();
            pbxProject.ReadFromFile(projectPath);

            string target = pbxProject.TargetGuidByName("Unity-iPhone");            
            pbxProject.SetBuildProperty(target, "ENABLE_BITCODE", "NO");

            pbxProject.WriteToFile (projectPath);
        }
    }
}
{% endhighlight %}

빌드 실행

## 참고자료
[https://support.unity3d.com/hc/en-us/articles/207942813-How-can-I-disable-Bitcode-support-]
[https://github.com/googleads/googleads-mobile-unity/issues/880]