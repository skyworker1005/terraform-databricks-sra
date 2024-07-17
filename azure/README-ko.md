# 보안 참조 아키텍처 템플릿

## 소개

Databricks는 수천 명의 고객과 협력하여 Databricks 플랫폼을 적절한 보안 기능과 함께 배포하여 아키텍처 요구 사항을 충족했습니다.

이 보안 참조 아키텍처(SRA) 리포지토리는 일반적으로 보안에 민감한 고객이 배포하는 일반적인 보안 기능을 통합한 템플릿으로 구현합니다.

## 구성 요소 분해 및 설명

이 섹션에서는 보안 참조 아키텍처에 포함된 각 구성 요소를 분해하여 설명합니다.

다양한 .tf 스크립트에서는 Databricks Terraform 문서에 대한 직접 링크를 포함했습니다. [공식 문서](https://registry.terraform.io/providers/databricks/databricks/latest/docs)를 여기서 확인할 수 있습니다.

### 인프라 배포

- **Vnet Injection**: [Vnet injection](https://learn.microsoft.com/en-us/azure/databricks/security/network/classic/vnet-injtec)은 Databricks 고객이 네트워크 구성을 제어하여 조직의 특정 클라우드 보안 및 거버넌스 표준을 준수할 수 있도록 합니다.

- **Private Endpoints**: Private Link 기술을 사용하여 [프라이빗 엔드포인트](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview)는 고객의 Vnet을 퍼블릭 IP 주소를 통과하지 않고 Azure 서비스에 연결하는 서비스입니다.

- **Private Link Connectivity**: Private Link는 Azure 서비스 간에 프라이빗 네트워크 경로를 제공합니다. [Private Link](https://learn.microsoft.com/en-us/azure/private-link/private-link-overview)는 고객의 데이터 플레인과 Databricks 제어 플레인 간의 통신이 퍼블릭 IP 주소를 통과하지 않도록 구성됩니다. 이 템플릿에서는 프론트 엔드 및 백엔드 Private Link가 설정됩니다.

- **Unity Catalog**: [Unity Catalog](https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog)는 파일, 테이블, 머신러닝 모델을 포함한 모든 데이터 및 AI 자산에 대한 통합 거버넌스 솔루션입니다. Unity Catalog는 중앙 집중식 정책, 감사, 계보 추적과 함께 세밀한 접근 제어를 제공합니다.

### 워크스페이스 배포 후

- **Admin Console Configurations**: [관리 콘솔](https://docs.databricks.com/administration-guide/admin-console.html) 내에서 위협 벡터를 줄이기 위해 제어할 수 있는 여러 가지 구성이 있습니다. 이 예제에서는 Terraform 템플릿을 사용하여 다양한 옵션을 활성화하고 비활성화합니다.

- **Cluster Tags and Pool Tags**: [클러스터 및 풀 태그](https://learn.microsoft.com/en-us/azure/databricks/administration-guide/account-settings/usage-detail-tags)를 사용하면 고객이 비용을 모니터링하고 Databricks 사용량을 조직의 비즈니스 단위 및 팀에 정확하게 할당할 수 있습니다.

## 추가 보안 권장 사항 및 기회

이 섹션에서는 추가 보안 권장 사항 및 기회를 설명하여 이 Terraform 스크립트에 구성할 수 없는 부분이나 개별 고객에 매우 특화된 부분(예: SCIM, SSO 등)을 다룹니다.

- **Segement Workspaces for Various Levels of Data Seperation**: Databricks에는 다양한 워크로드를 격리할 수 있는 여러 기능이 있지만, 주요 격리 방법은 민감한 워크로드를 다른 워크스페이스로 이동하는 것입니다.

- **Avoid Storing Production Datasets in Databricks File Store**: DBFS 루트는 워크스페이스의 모든 사용자가 액세스할 수 있으므로, 민감한 데이터를 저장하지 않도록 사용자에게 지시해야 합니다.

- **Single Sign-On, Multi-factor Authentication, SCIM Provisioning**: 대부분의 프로덕션 또는 엔터프라이즈 배포에서는 SSO 및 MFA를 사용하도록 워크스페이스를 설정합니다. 사용자 추가, 변경 및 삭제 시 SCIM을 사용하여 동기화하는 것이 좋습니다.

- **Backup Assets from the Databricks Control Plane**: Databricks는 재해 복구 서비스를 제공하지 않지만, 많은 고객은 Databricks 기능을 사용하여 다른 지역에 스탠바이 워크스페이스를 만듭니다.

- **Regularly Restart Databricks Clusters**: 클러스터를 재시작하면 최신 컴퓨팅 리소스 컨테이너 및 VM 호스트 이미지를 가져올 수 있습니다. 스트리밍 데이터 처리를 위해 사용되는 장기 실행 클러스터의 경우 정기적인 재시작이 중요합니다.

- **Evaluate Whether your Workflow requires using Git Repos or CI/CD**: 성숙한 조직은 CI/CD를 사용하여 코드 스캔, 권한 제어, 린팅 등을 수행하여 프로덕션 워크로드를 구축합니다. 매우 민감한 데이터가 분석될 때 CI/CD 프로세스를 통해 하드코딩된 시크릿을 스캔하는 것이 좋습니다.

## 시작하기

1. 이 리포지토리를 클론합니다.

2. [Terraform](https://developer.hashicorp.com/terraform/downloads)을 설치합니다.

3. `example.tfvars`를 작성하고 `tf` 디렉토리에 배치합니다.

4. `tf` 디렉토리로 이동합니다.

5. `terraform init`을 실행합니다.

6. `terraform validate`를 실행합니다.

7. `tf` 디렉토리에서 `terraform plan -var-file ../example.tfvars`를 실행합니다.

8. `terraform apply -var-file ../example.tfvars`를 실행합니다.

## 네트워크 다이어그램

![Architecture Diagram](https://cms.databricks.com/sites/default/files/inline-images/db-9734-blog-img-4.png)
