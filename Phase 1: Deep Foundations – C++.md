# Phase 1: Deep Foundations – C++ Memory & Passing

C++ قوي لأنه يمنحك التحكم الكامل في الذاكرة والأداء.
لكن هذا أيضًا سبب الأخطاء الشائعة. فهمك للميموري أساسي لتكون مطور محترف.

## 1️⃣ Memory Layout (Layout الذاكرة)

الذاكرة في C++ مقسمة إلى مناطق رئيسية:

1. Stack

مكان تخزين المتغيرات المحلية وبيانات استدعاء الدوال (Function call).

سريع جدًا لأنه يتم تخصيصه وإخلاؤه تلقائيًا عند نهاية scope.

مثال:

void foo() {
    int x = 10; // مخزن في stack
}


عندما تنتهي foo()، x يتم حذفه تلقائيًا.

2. Heap

لتخصيص الذاكرة ديناميكيًا أثناء runtime (new/delete).

أبطأ من stack ويحتاج إدارة يدوية.

مثال:

int* p = new int(5); // تخزين في heap
delete p;           // تحرير الذاكرة


خطأ شائع: نسيان delete → memory leak

3. Global

المتغيرات التي تعلن خارج أي دالة أو class.

Lifetime: طول البرنامج.

مثال:

int globalVar = 100;

4. Static

static variables موجودة في منطقة الذاكرة الخاصة بالبرنامج طوال فترة التنفيذ، لكن scope محدود حسب مكان الإعلان.

مثال:

void counter() {
    static int count = 0;
    count++;
    std::cout << count;
}


كل استدعاء يزيد نفس المتغير count لأنه موجود في الذاكرة طوال البرنامج.

5. Code Segment

يحتوي على الكود التنفيذي نفسه.

Read-only: لا يمكن تعديل الكود أثناء التشغيل.

## 2️⃣ Pointers vs References
Pointers (المؤشرات)

تخزن عنوان الذاكرة لشيء آخر.

يمكن أن تكون null، ويمكن إعادة التعيين (Reassignable).

مثال:

int x = 10;
int* p = &x; // p يشير إلى x
*p = 20;     // x أصبح 20

References (المرجع)

Alias لشيء موجود بالفعل.

يجب تهيئته عند الإعلان. لا يمكن أن يكون null ولا يمكن إعادة التعيين.

مثال:

int x = 10;
int& r = x;
r = 30; // x أصبح 30

الفرق العملي
Feature	Pointer	Reference
Null-able	✅	❌
Reassignable	✅	❌
Memory address	✅	✅
Syntax	*p / &x	r فقط

نصيحة: استخدم reference دائمًا إلا لو تحتاج pointer.

## 3️⃣ Pass by Value vs Reference vs Pointer
1. Pass by Value

نسخة من البيانات تنتقل للدالة

لا تؤثر على الأصل

مثال:

void foo(int x) { x = 100; }
int main() {
    int a = 50;
    foo(a);
    // a = 50 → لم يتغير
}

2. Pass by Reference

تمرير العنوان الأصلي ضمن reference

التغييرات تؤثر على الأصل

أسرع خاصة للكائنات الكبيرة

مثال:

void foo(int& x) { x = 100; }
int main() {
    int a = 50;
    foo(a);
    // a = 100 → تغير
}

3. Pass by Pointer

تمرير العنوان الأصلي ضمن pointer

يمكن أن يكون null

مثال:

void foo(int* x) { if(x) *x = 100; }
int main() {
    int a = 50;
    foo(&a);
    // a = 100 → تغير
}

Guidelines

Types صغيرة → Pass by value

Objects كبيرة → Pass by reference

Pointer → فقط عند الحاجة لإمكانية null أو إعادة التعيين

## 4️⃣ const – C++ Treasure

const يجعل شيء Immutable، يمنع التعديل الغير مقصود.

أمثلة

const variable

const int x = 10;


x لا يمكن تغييره بعد الإعلان.

const pointer

int a = 5;
int b = 10;
int* const p = &a; // p لا يمكن تغييره → لا يمكن إعادة التعيين
*p = 20;           // محتوى a يمكن تغييره


pointer to const

const int* p = &a;
*p = 30; // ❌ لا يمكن تعديل المحتوى
p = &b;  // ✅ يمكن إعادة التعيين


const reference

const int& r = a;
r = 30; // ❌ لا يمكن تعديل a من خلال r


استخدام const يقلل الأخطاء، ويجعل الكود أكثر أمانًا واحترافية.
