---
layout: post
title:  "django test attachment using SimpleUploadedFile"
date:   2016-11-11
categories: django
---

<br>  

> django 에서 첨부파일 기능의 테스트 코드를 작성하는데 모델의 객체를 생성하는 과정에서 test파일을 이용하는 방법을 몰라서 어려움을 겪었다. 이 포스팅에서는 FileField을 필드로 가지고 있는 모델을 테스트 하기 위해 upload된 상태의 test파일을 생성하는 방법에 대해서 다룬다. 첨부파일 기능은 modelform을 이용하여 구현했다.

<br>   

### models.py  

```python
class Attachment(models.Model):
    [...]
    attachment = models.FileField(blank=True, null=True)
```  

<br>  

### forms.py   

```python
class AttachmentForm(forms.ModelForm):
    class Meta:
        model = Attachment
        fields = ('attachment', )
```  

<br>  

### views.py  

```python
def new_attachment(request):
    if request.method == 'POST':
        attachment_form = AttachmentForm(request.POST, request.FILES)
        if attachment_form.is_valid():
            attachment = attachment_form.save()
            attachment.save()
            [...]
    else:
        attachment_form = AttachmentForm()

    [...]
```  

<br>  

### test code   

```python
from django.core.files.uploadedfile import SimpleUploadedFile

def test_can_save_a_attachment(self):
    first_uploaded_file = SimpleUploadedFile('saved_file.txt', open('test_file/test.txt', 'rb').read())
    Attachment.objects.create([...], attachment=first_uploaded_file)

    saved_attachments = Attachment.objects.all()
    self.assertEqual(saved_attachments.count(), 1)
```  

- SimpleUploadedFile을 이용해서 uploadedfile 객체를 생성하고 FileField 필드에 넣어준다.   

- `saved_file.txt` 은 upload될 파일의 이름을 입력한다.  

- `test_file/test.txt` 은 테스트 하고자 하는 파일의 상대경로를 입력한다.  
