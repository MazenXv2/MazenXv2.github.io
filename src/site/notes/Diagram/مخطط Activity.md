---
{"dg-publish":true,"permalink":"/diagram/mkhtt-activity/","dg-note-properties":{}}
---


```mermaid
flowchart TD
    %% ============================================
    %% Activity Diagram - Complaint Workflow
     ============================================
    %% Activity Diagram - Excavation Permit Workflow
    %% ============================================

    Start([ابدأ]) --> SubmitRequest[تقديم الطلب في البلدية]
    
    SubmitRequest --> ForwardToCoordination[إحالة إلى مكتب التنسيق]
    
    ForwardToCoordination --> CoordinationReview[دراسة الطلب في مكتب التنسيق]
    
    CoordinationReview --> CheckServices{موافقات الجهات الخدمية}
    
    CheckServices -->|غير مكتملة| RequestApprovals[طلب موافقات الجهات]
    RequestApprovals --> CheckServices
    
    CheckServices -->|مكتملة| SendToCommittee[إرسال إلى اللجنة]
    
    SendToCommittee --> CommitteeInspection[كشف اللجنة]
    
    CommitteeInspection --> CalculateFees[حساب الرسوم]
    
    CalculateFees --> SendToFinance[إرسال إلى المالية]
    
    SendToFinance --> PayFees[دفع الرسوم في المالية]
    
    PayFees --> VerifyPayment{تأكيد الدفع}
    
    VerifyPayment -->|غير مدفوع| PayFees
    
    VerifyPayment -->|مدفوع| SendToDirector[إحالة إلى مدير التنفيذ والخدمات]
    
    SendToDirector --> DirectorApproval1{موافقة المدير}
    
    DirectorApproval1 -->|غير موافق| Reject[رفض الطلب]
    Reject --> NotifyCitizen[إشعار المواطن بالرفض]
    NotifyCitizen --> End1([End])
    
    DirectorApproval1 -->|موافق| DirectorSign[توقيع مدير التنفيذ والخدمات]
    
    DirectorSign --> HeadApproval{موافقة رئيس المدير}
    
    HeadApproval -->|غير موافق| Reject2[رفض الطلب]
    Reject2 --> NotifyCitizen2[إشعار المواطن بالرفض]
    NotifyCitizen2 --> End2([End])
    
    HeadApproval -->|موافق| HeadSign[توقيع رئيس المدير]
    
    HeadSign --> IssuePermit[إصدار إجازة الحفر]
    
    IssuePermit --> PermitPrint[طباعة الإجازة]
    
    PermitPrint --> DeliverToCitizen[تسليم الإجازة للمواطن]
    
    DeliverToCitizen --> End3([End])
```