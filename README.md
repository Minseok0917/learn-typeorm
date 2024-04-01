### Decorators

| name                               | description                                                     |
| ---------------------------------- | --------------------------------------------------------------- |
| Entity Decorators                  |                                                                 |
| `@Entity()`                        | 클래스를 엔티티로 선언합니다. 테이블에 대응됩니다.              |
| `@ViewEntity()`                    | 뷰 엔티티를 정의할 때 사용합니다. 데이터베이스 뷰에 대응됩니다. |
| Column Decorators                  |
| `@Column()`                        | 필드를 테이블의 컬럼으로 매핑합니다.                            |
| `@PrimaryColumn()`                 | 프라이머리 키 컬럼을 정의합니다.                                |
| `@PrimaryGeneratedColumn()`        | 자동으로 생성되는 프라이머리 키를 의합니다.                     |
| `@ObjectIdColumn()`                | MongoDB의 Object ID 컬럼을 위해 사용됩니다.                     |
| `@CreateDateColumn()`              | 엔티티가 생성될 때의 날짜/시간을 자동으로 기록합니다.           |
| `@UpdateDateColumn()`              | 엔티티가 수정될 때의 날짜/시간을 자동으로 기록합니다            |
| `@DeleteDateColumn()`              | 엔티티가 소프트 삭제될 때의 날짜/간을 기록합니다.               |
| `@VersionColumn()`                 | 엔티티의 버전을 관리하는 컬럼입니다.                            |
| `@Generated()`                     | 데이터베이스에서 자동으로 생성되는 값을 갖는 컬럼입니다.        |
| `@VirtualColumn()`                 | 실제 데이터베이스 컬럼이 아닌, 가상 컬럼을 정의합니다.          |
| Relation Decorators                |
| `@OneToOne()`                      | 일대일 관계를 정의합니다.                                       |
| `@ManyToOne()`                     | 다대일 관계를 정의합니다.                                       |
| `@OneToMany()`                     | 일대다 관계를 정의합니다.                                       |
| `@ManyToMany()`                    | 다대다 관계를 정의합니다.                                       |
| `@JoinColumn()`                    | 관계의 소유측에서 외래 키 컬럼을 지정할 때 사용합다.            |
| `@JoinTable()`                     | 다대다 관계에서 연결 테이블을 지정할 때 사용합니다.             |
| `@RelationId()`                    | 관계의 ID를 가져오기 위한 필드를 정의할 때 사용합니다.          |
| Subscriber and Listener Decorators |
| `@AfterLoad()`                     | 엔티티가 로드된 직후에 실행됩니다.                              |
| `@BeforeInsert()`                  | 새 엔티티가 저장되기 전에 실행됩니.                             |
| `@AfterInsert()`                   | 새 엔티티가 저장된 후에 실행됩니다.                             |
| `@BeforeUpdate()`                  | 엔티티가 업데이트되기 전에 실행됩니.                            |
| `@AfterUpdate()`                   | 엔티티가 업데이트된 후에 실행됩니다.                            |
| `@BeforeRemove()`                  | 엔티티가 삭제되기 전에 실행됩니.                                |
| `@AfterRemove()`                   | 엔티티가 삭제된 후에 실행됩니다.                                |
| `@BeforeSoftRemove()`              | 엔티티가 소프트 삭제되기 전에 실행됩니.                         |
| `@AfterSoftRemove()`               | 엔티티가 소프트 삭제된 후에 실행됩니다.                         |
| `@BeforeRecover()`                 | 소프트 삭제된 엔티티가 복구되기 전에 실행됩니.                  |
| `@AfterRecover()`                  | 소프트 삭제된 엔티티가 복구된 후에 실행니다.                    |
| `@EventSubscriber()`               | 이벤트 구독자를 정의할 때 사용합니다.                           |
| Other Decorators                   |
| `@Index()`                         | 테이블의 인덱스를 정의할 때 사용합니다.                         |
| `@Unique()`                        | 고유한 제약 조건을 정의할 때 사용합니.                          |
| `@Check()`                         | 체크 제약 조건을 정의할 때 사용합니다.                          |
| `@Exclusion()`                     | 배타적 제약 조건을 정의할 때 사용합니다.                        |

### Repository APIS

- repository.manager
- repository.metadata
- repository.queryRunner
- repository.target
- repository.createQueryBuilder

  - getOne : 단일 결과
  - getOneOrFail : 단일 결과가 없을 경우 EntityNotFoundError
  - getMany : 모든 결과
  - getRawOne
  - getRawMany
  - getCount
  - where
  - setParameter
  - andWhere
  - orWhere
  - Bracket : 괄호

  ```typescript
  createQueryBuilder("user")
    .where("user.registered = :registered", { registered: true })
    .andWhere(
      new Brackets((qb) => {
        qb.where("user.firstName = :firstName", {
          firstName: "Timber",
        }).orWhere("user.lastName = :lastName", { lastName: "Saw" });
      })
    );
  ```

  - Not Bracket : 부정 괄호

  ```typescript
  createQueryBuilder("user")
    .where("user.registered = :registered", { registered: true })
    .andWhere(
      new NotBrackets((qb) => {
        qb.where("user.firstName = :firstName", {
          firstName: "Timber",
        }).orWhere("user.lastName = :lastName", { lastName: "Saw" });
      })
    );
  ```

  - having
  - andHaving
  - orHaving
  - orderBy
  - andOrderBy
  - distinctOn
  - groupBy
  - addGroupBy
  - limit
  - offset
  - leftJoinAndSelect : join 에 조건 추가 가능
  - innerJoinAndSelect
  - innerJoin
  - leftJoinAndMapOne
  - printSql
  - select
  - stream
  - take
  - skip
  - setLock
  - setOnLocked
  - useIndex
  - maxExecutionTime
  - qb.subQuery
  - qb.getQuery
  - from
  - addSelect
  - @Column({select:false})
  - withDeleted
  - addCommonTableExpression
  - InsertQueryBuilder
  - UpdateQueryBuilder
  - timeTravelQuery
  - getQueryAndParameters

- repository.hasId
- repository.getId
- repository.merge
- repository.preload
- repository.save
- repository.insert
- repository.update
- repository.upsert
- repository.delete
- repository.softDelete
- repository.softRemove
- repository.increment
- repository.decrement
- repository.exits
- repository.exitsBy
- repository.count
- repository.countBy
- repository.sum
- repository.average
- repository.minimum
- repository.maximum
- repository.find
- repository.findBy
- repository.findAndCount
- repository.findAndCountBy
- repository.findOne
- repository.findOneBy
- repository.findOneOrFail
- repository.query
- repository.clear
