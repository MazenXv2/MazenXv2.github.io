---
{"dg-publish":true,"permalink":"/diagram/mkhtt-activity/","dg-note-properties":{}}
---


# معالجة الشكاوى
```mermaid
flowchart TD
    %% ============================================
    %% Activity Diagram - Complaint Workflow
     ============================================
    %% Activity Diagram - Excavation Permit Workflow
    %% ============================================

    Start([ابدأ]) --> SubmitRequest[تقديم طلب رخصة حفر]
    
    SubmitRequest --> ForwardToDirector[إحالة إلى رئيس المديرية]
    
    ForwardToDirector --> DirectorDecision{قرار رئيس المديرية}
    
    DirectorDecision -->|رفض| Reject[رفض الطلب]
    Reject --> NotifyCitizen[إشعار المواطن بالرفض]
    NotifyCitizen --> End1([End])
    
    DirectorDecision -->|قبول| AssignToMonitor[تحويل إلى المراقب المختص]
    
    AssignToMonitor --> MonitorInspection[الكشف الميداني من المراقب]
    
    MonitorInspection --> MonitorDecision{موافقة المراقب}
    
    MonitorDecision -->|رفض| Reject2[رفض الطلب]
    Reject2 --> NotifyCitizen2[إشعار المواطن بالرفض]
    NotifyCitizen2 --> End2([End])
    
    MonitorDecision -->|موافقة| CheckServices{موافقات الجهات الخدمية}
    
    CheckServices -->|رفض| Reject3[رفض الطلب]
    Reject3 --> NotifyCitizen3[إشعار المواطن بالرفض]
    NotifyCitizen3 --> End3([End])
    
    CheckServices -->|موافقة| CalculateFees[حساب الرسوم]
    
    CalculateFees --> SendToFinance[إرسال إلى المالية]
    
    SendToFinance --> PayFees[دفع الرسوم في المالية]
    
    PayFees --> VerifyPayment{تأكيد الدفع}
    
    VerifyPayment -->|غير مدفوع| PayFees
    
    VerifyPayment -->|مدفوع| IssuePermit[إصدار إجازة الحفر]
    
    IssuePermit --> PermitPrint[طباعة الإجازة]
    
    PermitPrint --> DeliverToCitizen[تسليم الإجازة للمواطن]
    
    DeliverToCitizen --> End4([End])
```


# طلب تسليم مواد + شراء مواد

```mermaid
flowchart TD
    %% ============================================
    %% Activity Diagram - Material Request & Procurement
    %% ============================================

    Start([ابدأ]) --> RequestMaterials[طلب تسليم مواد]
    
    RequestMaterials --> CheckAvailability{فحص التوفر في المستودع}
    
    CheckAvailability -->|متوفرة| ApproveRequest[اعتماد الطلب]
    ApproveRequest --> DeliverMaterials[تسليم المواد]
    DeliverMaterials --> RequestExit1[طلب إخراج]
    RequestExit1 --> End1([End])
    
    CheckAvailability -->|غير متوفرة| NotifyDirector[إشعار المدير بعدم التوفر]
    
    NotifyDirector --> DirectorDecision{موافقة المدير على الشراء}
    
    DirectorDecision -->|رفض| RejectRequest[رفض الطلب]
    RejectRequest --> NotifyRequester[إشعار مقدم الطلب بالرفض]
    NotifyRequester --> End2([End])
    
    DirectorDecision -->|موافقة| CreatePurchaseRequest[إنشاء طلب شراء]
    
    CreatePurchaseRequest --> SendToProcurement[إحالة إلى لجنة الشراء]
    
    SendToProcurement --> ProcurementReview[دراسة طلب الشراء]
    
    ProcurementReview --> PreparePurchaseOrder[إعداد أمر الشراء]
    
    PreparePurchaseOrder --> RequestMunicipalityApproval[طلب موافقة البلدية]
    
    RequestMunicipalityApproval --> RequestBudgetAllocation[طلب اعتماد من البلدية -تخصيص مال]
    
    RequestBudgetAllocation --> SelectContractor[اختيار المتعهد -سيتم شراء الأغراض منه]
    
    SelectContractor --> SupplierProcess[عملية التوريد]
    
    SupplierProcess --> ReceiveMaterials[استلام المواد]
    
    ReceiveMaterials --> QualityInspection[فحص الجودة]
    
    QualityInspection --> QualityDecision{جودة المواد}
    
    QualityDecision -->|غير مطابقة| ReturnToSupplier[إعادة المواد للمورد]
    ReturnToSupplier --> SupplierProcess
    
    QualityDecision -->|مطابقة| RequestReceipt[طلب استلام]
    
    RequestReceipt --> RequestEntry[طلب إدخال]
    
    RequestEntry --> NotifyRequester3[إشعار مقدم الطلب بتوفر المواد]
    
    NotifyRequester3 --> DeliverMaterials2[تسليم المواد]
    
    DeliverMaterials2 --> RequestExit2[طلب إخراج]
    
    RequestExit2 --> End3([End])
```