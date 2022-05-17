작업명세파일 변수 종류
================================

* **$(Cluster) or $(ClusterId)** 작업이 가지고 있는 고유 번호로 새 작업 마다 하나씩 증가하는 숫자 입니다.
* **$(Process) or $(ProcId)** 작업 클러스터 내에서 각각 가지고 있는 고유 번호 입니다. 0부터 시작합니다.
* **$F[qpdnxba](filename)** 파일 이름을 기반으로 소문자 옵션과 결합하여 다양한 값을 출력합니다.
 
 * –p: 파일이 위치한 경로를 출력
 * –q: 결과값을 큰따옴표로 묶어서 출력
 * –a: q옵션과 함께 사용되며 결과값을 작은 따옴표로 묶어서 출력 
 * –d: 파일이 위치한 디렉토리를 출력
 * –b: d옵션과 사용할 때 디렉토리 결과값 중 마지막 슬래쉬를 제거. x옵션과 사용할때 확장자 앞의 점을 제거 
 * –n: 확장자를 제외한 파일 이름을 출력
 * –x: 파일의 확장자를 점을 포함하여 출력
  
 **사용예시** $(file)이 */home/work/recipe.lst* 와 같을때
  ∗ $Fp(file) → /home/work/
  ∗ $Fqp(file) → “/home/work” 
  ∗ $Fqpa(file) → ‘/home/work’ 
  ∗ $Fd(file) → work/
  ∗ $Fdb(file) → work
  ∗ $Fn(file) → recipe
  ∗ $Fx(file) → .lst
  ∗ $Fnx(file) → recipe.lst
