---
tags: attack ransomware
toc: True
---
# Ransomware
* Ransomware is a type of malware that restricts access to the infected computer system, and demands that the user pays a ransom to the attacker to remove the restriction. (랜섬웨어는 감염된 컴퓨터 시스템에 대한 접근을 제한하는 악성코드의 일종으로, 사용자가 제한을 해제하기 위해 공격자에게 몸값을 지불하도록 요구한다.)
* Some forms of ransomware encrypt files on the system’s local storage, which become almost impossible to decrypt without paying the ransom for the decryption key; while some may simply lock the system and display messages intended to coax the user into paying. (일부 형태의 랜섬웨어는 시스템의 로컬 저장소에 있는 파일을 암호화합니다. 이 파일은 암호 해독 키에 대한 몸값을 지불하지 않고는 암호 해독이 거의 불가능합니다. 일부는 단순히 시스템을 잠그고 사용자가 지불하도록 유도하는 메시지를 표시할 수 있습니다.)
* Ransomware can be classified into two classes: Class A ransomware overwrites the contents of the original file, so the deleted file cannot be restored.(랜섬웨어는 크게 두 가지로 분류할 수 있습니다. A급 랜섬웨어는 원본 파일의 내용을 덮어쓰므로 삭제된 파일을 복원할 수 없습니다)
* In this class, encrypted filenames are the same as the original ones. (이 클래스에서 암호화된 파일 이름은 원래 파일 이름과 동일합니다.)
* Class B ransomware makes another encrypted file (for example, B.txt.crypt) using the original file then deletes the original file after encryption.(클래스 B 랜섬웨어는 원본 파일을 이용하여 또 다른 암호화된 파일(예: B.txt.crypt)을 만든 다음 암호화 후 원본 파일을 삭제합니다.)
* The encrypted files have other file extensions and sometimes the original files can be restored because they are not overwritten. (암호화된 파일에는 다른 파일 확장자가 있으며 때로는 원본 파일을 덮어쓰지 않기 때문에 원래 파일을 복원할 수 있습니다.)
* Class B ransomware is easy to detect because files having a specific file extension are created.(클래스 B 랜섬웨어는 특정 파일 확장자를 가진 파일이 생성되기 때문에 탐지하기 쉽습니다.)
* Whereas, class A is more serious because the original files cannot be restored in practice, so we will concentrate on class A ransomware in this study.(반면 클래스 A는 실제로 원본 파일을 복원할 수 없기 때문에 더 심각하므로 본 연구에서는 클래스 A 랜섬웨어에 집중할 것입니다.)
