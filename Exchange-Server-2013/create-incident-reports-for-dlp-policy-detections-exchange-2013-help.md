﻿---
title: 'DLP 정책 감지에 대 한 문제 보고서 만들기: Exchange 2013 Help'
TOCTitle: DLP 정책 감지에 대 한 문제 보고서 만들기
ms:assetid: 8e807f94-384c-43f5-be6f-06c5587175a0
ms:mtpsurl: https://technet.microsoft.com/ko-kr/library/JJ150534(v=EXCHG.150)
ms:contentKeyID: 50482300
ms.date: 05/22/2018
mtps_version: v=EXCHG.150
ms.translationtype: MT
---

# DLP 정책 감지에 대 한 문제 보고서 만들기

 

_**적용 대상:** Exchange Online, Exchange Server 2013_

_**마지막으로 수정된 항목:** 2015-04-07_

Exchange Server 2013 DLP 정책 규칙 집합 내에서 문제 보고서 생성 동작을 설정할 수 있습니다. 또한 누구에 게 보고서를 보낼 수 있어야 하 고 원본 메시지와 함께 수행할 작업을 나타낼 수 있습니다. 문제 보고서의 다음 정보를 포함할 수 있습니다.

## 문제 관리 보고서의 내용

**문제 보고서 생성** 작업을 사용하면 사용자가 문제 관리 사서함에 문제 보고서를 보낼 수 있습니다. 정책 내에서 **문제 보고서 생성** 작업이 적용되면 각 메시지에 대해 문제 보고서가 하나씩만 생성됩니다.

다음은 문제 보고서 템플릿의 전체 줄 이름 목록입니다. 형식 열에서는 보고서의 각 필드를 인식하는 방법에 대해 설명합니다. 선택적 필드 열에서는 각 규칙 일치를 위해 보고서에 포함될 수 없는 필드를 지정합니다. DLP 관련 열에는 DLP 기능의 결과로 존재하는 필드가 표시됩니다.


<table>
<colgroup>
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>줄</strong> <strong>이름</strong></p></td>
<td><p><strong>설명</strong></p></td>
<td><p><strong>형식</strong></p></td>
<td><p><strong>선택적 필드</strong></p></td>
<td><p><strong>DLP 관련</strong></p></td>
</tr>
<tr class="even">
<td><p>메시지 ID</p></td>
<td><p>원래 보낸 메시지의 ID</p></td>
<td><p>메시지 ID: 메시지의 ID</p></td>
<td><p>필수</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="odd">
<td><p>보낸 사람</p></td>
<td><p>원본 메시지를 실제로 보낸 사람</p></td>
<td><p>보낸 사람 : 보낸 사람의 전자 메일 주소</p></td>
<td><p>필수</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="even">
<td><p>제목</p></td>
<td><p>원본 메시지의 제목</p></td>
<td><p>제목: 최종 사용자가 입력한 제목 문자열</p></td>
<td><p>필수</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="odd">
<td><p>받는 사람</p></td>
<td><p>원본 메시지의 받는 사람</p>
<p>각 받는 사람 줄에는 받는 사람이 한 명만 포함되고 문제 보고서에는 받는 사람이 최대 10명까지 표시될 수 있습니다. 받는 사람이 추가로 있을 경우 다음 받는 사람 줄에 남은 받는 사람 수가 표시됩니다.</p></td>
<td><p>받는 사람: 받는 사람의 전자 메일 주소</p></td>
<td><p>필수</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="even">
<td><p>참조</p></td>
<td><p>원본 메시지의 참조 전자 메일 주소. 이 줄은 옵션입니다.</p>
<p>각 참조 줄에는 참조 전자 메일 주소가 하나씩만 포함되고 문제 보고서에는 참조 전자 메일 주소가 최대 10개까지 표시될 수 있습니다. 참조 주소가 추가로 있을 경우 다음 참조 줄에 남은 참조 전자 메일 주소 수가 표시됩니다.</p></td>
<td><p>참조: 참조 받는 사람의 전자 메일 주소</p></td>
<td><p>옵션</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="odd">
<td><p>숨은 참조</p></td>
<td><p>원본 메시지의 숨은 참조 전자 메일 주소. 이 줄은 옵션입니다.</p>
<p>각 숨은 참조 줄에는 숨은 참조 전자 메일 주소가 하나씩만 포함되고 문제 보고서에는 숨은 참조 주소가 최대 10개까지 표시될 수 있습니다. 숨은 참조 전자 메일 주소가 추가로 있을 경우 다음 숨은 참조 줄에 남은 숨은 참조 전자 메일 주소 수가 표시됩니다.</p></td>
<td><p>숨은 참조: 숨은 참조 받는 사람의 전자 메일 주소</p></td>
<td><p>옵션</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="even">
<td><p>심각도</p></td>
<td><p>적중된 규칙의 감사 심각도. 여러 규칙이 적용된 경우 가장 높은 심각도를 표시합니다.</p></td>
<td><p>심각도: 낮음, 중간 또는 높음</p></td>
<td><p>옵션</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="odd">
<td><p>다시 정의</p></td>
<td><p>재정의가 메시지에 대해 보고되었는지 여부와 제공된 경우 재정의의 근거를 표시합니다.</p></td>
<td><p>다시 정의: 예, 근거: 정보 근로자가 입력한 근거 문자열</p></td>
<td><p>옵션</p></td>
<td><p>예</p></td>
</tr>
<tr class="even">
<td><p>가양성</p></td>
<td><p>가양성이 메시지에 대해 보고되었는지 여부를 표시합니다.</p></td>
<td><p>가양성: 예</p></td>
<td><p>옵션</p></td>
<td><p>예</p></td>
</tr>
<tr class="odd">
<td><p>데이터 분류</p></td>
<td><p>원본 메시지에서 찾은 검색된 데이터 분류. 이 줄은 옵션입니다.</p>
<p>각 데이터 분류 줄에는 검색된 분류가 해당 분류의 개수, 신뢰도 및 권장 최소 신뢰 수준과 함께 하나씩만 포함되고 문제 보고서에는 검색된 분류가 최대 5개까지 표시됩니다. 검색된 분류가 선호도인 경우 개수 값은 적용되지도 않고 표시되지도 않습니다.</p></td>
<td><p>데이터 분류: 중요한 정보 유형, 개수: 메시지에서 찾은 중요한 정보의 인스턴스, 신뢰도: 백분율 값, 권장 최소 신뢰: 백분율 값</p></td>
<td><p>옵션</p></td>
<td><p>예</p></td>
</tr>
<tr class="even">
<td><p>적중된 규칙</p></td>
<td><p>원본 메시지와 일치하는 규칙을 모두 표시합니다.</p>
<p>적중된 규칙 이름, 규칙이 상주하는 DLP 정책(옵션), 규칙으로 인해 메시지에서 수행된 작업, 규칙이 적중되도록 만든 규칙 내 데이터 분류, 규칙 정의 등이 포함됩니다.</p></td>
<td><p>적중된 규칙: 규칙 이름, DLP 정책: DLP 정책 이름(해당하는 경우), 작업: 단일 작업, 데이터 분류: 중요한 정보 유형, 정의: 규칙 정의(해당하는 경우)</p></td>
<td><p>필수</p></td>
<td><p>아니요</p></td>
</tr>
<tr class="odd">
<td><p>ID 일치</p></td>
<td><p>일치하는 데이터 분류, 메시지에서 정확히 일치한 내용, 데이터 분류 일치의 기본 증거 등을 표시합니다. 이 줄은 옵션입니다.</p></td>
<td><p>ID 일치: 중요한 정보 유형, 값: 중요한 데이터의 실제 값, 컨텍스트: 메시지에서 중요한 데이터 주위의 텍스트</p></td>
<td><p>옵션</p></td>
<td><p>예</p></td>
</tr>
</tbody>
</table>


## 자세한 내용

[DLP 정책 검색 보고서 보기](view-dlp-policy-detection-reports-exchange-2013-help.md)

[데이터 손실 방지](https://docs.microsoft.com/ko-kr/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention)

