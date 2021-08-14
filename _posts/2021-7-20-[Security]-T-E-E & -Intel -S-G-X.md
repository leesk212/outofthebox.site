---
tags: security-defense computer-architecture
toc: True
---

# TEE(Trusted Execution Environment)

TEE(Trusted Execution Environment)는 Main Processor(CPU) 내부에 있는 안전한 영역이며, 이곳에 있는 Code 와 Data는 안전하게 보호된다는 것을 의미합니다.

 

TEE 기능을 Processor에서 구현한 것으로, Intel의 SGX 와 ARM의 TrustZone이 알려져 있습니다..

 

TrustZone 기술을 기반으로 한 TEE 를 “TrustZone-based TEE” 라고 부릅니다. ARM CPU를 사용하고 있는 Android Phone에서 지원되고 있습니다. 

 

민감한 data를 안전한 영역에 보관하는 방식으로, TEE 외에 TPM 과 HSM도 있으므로 간단히 언급하고자 합니다.

 

## TEE :  CPU(Processor) 내부에서 수행

TPM(Trusted Platform Module) : 시스템의 한 Component로 존재, 주로 Notebook PC에서 암호키를 hardware적으로 안전하게 보관하기 위한 장치로 사용되고 있습니다.

HSM(Hardware Security Module) : 독립된 전용 장비이며, FIPS 140-2 level 3 이상의 인증을 받은 장비인 경우는 Physical Attack 기능 조차도 방지하며, Level 4 인증을 받은 장비는 Physical Attack의 범위가 훨씬 넓습니다.

 

 

# SGX(Software Guard Extensions)

SGX(Software Guard Extensions)는 Intel에서 개발한 기술로, 2015년 Skylake micro-architecture를 기반으로 하는 제6세대 Intel Core Processor에서 처음 소개되었습니다. 이 기능을 사용하려면 BIOS/UEFI에서 이 기능을 enable 시켜 주어야 합니다. CPU에서 SGX 지원 여부는, CPUID의 특정 bit를 통하여 확인할 수 있다고 합니다.

 

SGX는 Enclave라 불리는 완전히 분리된 Secure한 영역에서 Application을 running 시킬 수 있는 Intel x86 Architecture의 확장 기능입니다.

 

간단히 기술하면, SGX는 Intel CPU 안에 들어있는 mini-HSM(Hardware Security Module)이라고 보면 됩니다.

 

Enclave는 private memory region으로 Protected Memory 영역을 의미하며, 같은 시스템에서 동작하는 다른 Process들에서는 접근할 수 없는 분리된 메모리 영역입니다. Code가 실행되는 영역이기 때문에 Secure Execution Environment 이기도 합니다. Enclave 영역에서 running 되고 있는 Program은, 같은 System상에서 running되고 있는 다른 Application과 완전히 분리되어 있을 뿐 아니라, OS 또는 Hypervisor 와도 완전히 분리되어 있습니다. 이것이 의미하는 바는, System 관리자도, Enclave 영역에서 동작하는 Program이 일단 running된 이후에는, Access할 수 없다는 것입니다. Enclave를 SGX Memory라고도 부릅니다.

 

또한 Enclave 영역에 대한 Physical 공격을 방지하기 위하여, Enclave 영역에 속한 메모리 내용은 encryption되어 있습니다. 오직 CPU에 의해서만 on-the-fly로 decryption됩니다. 여기서 사용되는 암호키는 CPU 제조 시에 생성되며, 오직 CPU만 알고 있으며, Intel 회사에서는 모른다고 합니다.

 

Enclave 영역에서 running 되고 있는 Code 과 Data를 모두 합하여 그냥 Enclave라고 부르기도 하며, CPU가 Enclave 영역에서 동작되는 상태를 Enclave Mode라고 합니다.

 

Enclave 영역에 속해 있는 data는 Enclave 영역에서 running 되고 있는 Code만이 access할 수 있습니다.  Enclave 영역에서  running되고 있는 Code는, 자신의 Host Application에서 access하는 memory도 access할 수 있다고 합니다

 

SGX 기능을 사용하여 Application을 개발하는 경우는, Application을 두 부분으로 나눌 수 있습니다. 즉 Enclave 영역에서 동작되는 Secure Part 와 Normal Memory에서 동작되는 None-secure Part로 나눌 수 있습니다. Enclave를 Create한 Host Application에서만 자신의 Enclave에 Call할 수 있습니다. 

 

아래 그림은 전체적인 개념을 보여 줍니다.

![image](https://user-images.githubusercontent.com/67637935/126315532-6fc0b9ae-59e0-4c9d-b1ed-d0038337094e.png)

 

Intel의 SGX 기술은 Processor에, 강력한 암호 기능을 가진 Memory 분리 기능을 구현한 것입니다. Processor는 각 Enclave에 할당된 Memory들을 tracking하고 있기 때문에, Enclave를 Create한 Host Application만이 자신의 Enclave영역에 들어갈 수 있습니다. 이러한 기능을 구현하기 위해 18개의 새로운 instruction(명령어)를 추가하였습니다. 13개는 Supervisor가 사용하는 명령어이며, 5개는 User가 사용하는 명령어 입니다.

 

참고로, 아래는 User가 사용할 수 있는 5개에 대한 명령어와 이에 대한 설명입니다.

*   EENTER  : Enter an enclave

*   EEXIT    : Exit an enclave

*   EGETKEY : Create a cryptographic key

*   EREPORT : Create a cryptographic report

*   ERESUME : Re-enter an enclave

 

Enclave에서는 Sealing 과 Attestation 이라 불리는 2개의 핵심적인 기능을 제공하고 있습니다.

 

Sealing : Enclave 가 시작되면, 그 속에 있는 Code 와 Data은 보호되지만, Enclave가 stop 하면, data는 사라지게 됩니다. 따라서 Enclave가 Data를 Enclave 외부의 안전한 곳에 저장하는 것을 Sealing 이라고 합니다. 이러한 기능을 구현하기 위해, Enclave는 EGETKEY 명령어를 사용하여 Seal Key를 가져온 후, Seal Key로 Data를 encryption시켜서 저장시킵니다. Seal Key는 MRENCLAVE(Enclave의 Position 과 내용 등을 Hash한 값) 또는 MRSIGNER(Enclave Author의 Public-Key를 Hash한 값)를 근거로 derive됩니다. 따라서 두 개의 다른 Enclave가 동일한 Seal Key를 가지지는 않습니다. 하지만 동일한 Enclave의 두 개의 버전이 있는 경우, “MRENCLAVE 사용 시(Enclave Identity)”는 다른 Key를 가지지만, “MRSIGNER(Signer Identity)” 사용 시는 같은 Key를 가지게 됩니다. 

 

Attestation : Third-party에게 Enclave 안에서 동작되는 Code를 신뢰할 수 있도록 증명하는 기능입니다. Enclave의 code 와 data는 시작되기 전에 plain-text 상태이며, 시작된 이후에도 부정 조작되지 않았다는 것을 증명하는 것입니다. 두 가지 종류의 Attestation이 존재합니다.

Local Attestation : 동일한 Platform 상에 있는 2개의 Enclave 사이에서 수행하는 Attestation 과정

Remote Attestation : “Enclave” 와 “동일한 Platform에 있지 않은 Third-Party”사이의 Attestation 과정. 따라서 사용자는 Remote Attestation 기능을 사용하여, 자신의 Application이 Secure한 Enclave안에서 동작한다는 것을 Third-party에게 증명할 수 있습니다.

 

Intel에 의하면, SGX는 Side-Channel Attack을 방지하는 기능이 없기 때문에, Enclave 안에서 동작하는 Code는 Side-Channel resistant을 하도록 작성되어야 한다고 합니다.

 

2020.11.13, Birmingham 대학교의 연구원 그룹은, VoltPillager 라는 저렴한 장비를 사용하여, CPU Core Voltage를 조작하여, Intel SGX Enclave 내부에 있는 data를 access하였다고 발표하였습니다.  VoltPillager는 CPU와 voltage Regulator(조정기) 사이에 있는 Serial Voltage Identification(SVID) bus에 메시지를 주입하게 해 주는 하드웨어 장비로 $30 정도면 만들 수 있다고 합니다. Intel에서는, SGX는 하드웨어를 기반으로 한 Physical 공격을 고려하지 않고 만든 기술이다 라는 입장을 내놓았다고 합니다. 참고로, FIPS 140-2 level 4 인증을 받은 HSM 장비인 경우는 다양한 종류의 Physical Attack을 방어하는 기능을 가지고 있습니다.

 

 

## SGX Application

SGX 기능을 사용하는 Application을 개발하려면, Intel SGX SDK를 사용해야 합니다. SGX SDK에는 Simulator도 함께 제공하고 있기 때문에, SGX를 지원하는 CPU가 없이도 개발할 수 있고 debugging도 가능하다고 합니다.

 

SGX Application은 두 부분으로 나누어 개발해야 합니다. 민감한 data를 다루는 Application part는 Enclave 내에서 동작하는 Trusted part에 두어야 합니다.  이를 위해 Visual Studio에서 2개의 Project를 만들어 각각 개발을 해야 합니다.

 

Trusted Part 와 Untrusted Part 사이의 Interface를 위해 e-calls 와 o-calls 이 존재합니다.

e-calls : Enclave 내부에 있는 function인데, Untrusted Part로부터 call됩니다.

o-calls : Untrusted Part에 있는 function인데, Enclave로부터 call됩니다.

 

Message를 Sign하는 Application을 개발하는 경우로 예를 들면, 암호키 관련 기능 즉 key generation, signing, verification 은 모두 Enclave에서 수행되도록 하고, 암호키는 Enclave안에 저장하던가 아니면 Sealed 된 상태로 저장 장치 저장하도록 해야 합니다. 개념적으로 보면, HSM을 사용하여 Application을 개발하는 것과 유사합니다. HSM인 경우는 PKCS#11 이라고 부르는 표준 API를 제공하기 때문에, HSM을 사용하는 Application 즉 Untrusted Part에선 PKCS#11 API만 Call하면 모든 암호 관련 작업은 HSM내부에서 수행됩니다. SGX인 경우는 Enclave가 제공하는 표준 API는 없지만, 개발자가 API에 해당되는 e-calls 함수들을 Enclave내부에 마음대로 둘 수 있기 때문에, 확장성면에선 더 좋다고 할 수 있습니다. SGX SDK에선 개발에 필요한 암호기능 관련 function을 포함한 다양한 function들을 Library 형태로 제공하고 있다고 합니다.

 

Untrusted Part의 기본 구성을 C 언어 표현해 보면 아래와 같습니다. 


```c
main()
{

      /* Enable SGX */

    ret = sgx_enable_device(&sgx_device_status);

 

      /* 성공적으로 Enclave가 Create되면, 이후에는 eid 를 가지고 call 합니다. */

    ret = sgx_create_enclave(ENCLAVE_FILE_NAME, SGX_DEBUG_FLAG, &token, &updated, &eid, NULL) 

 

      /* Enclave를 초기화 시키는 작업입니다 */ 

    ret = esv_init(eid, &res, sealed_data_name) ;

 

     /* Enclave 사용을 종료합니다. */

    ret = sgx_destroy_enclave(…);

 

}
```
## Ref:
* https://m.blog.naver.com/aepkoreanet/222155921859
