**Action** `<Module>.<name> 'script'` - CLASS ActionInfo. BASE CLASS Parameter: This concept is not necessary for defining a filter. Any data structure (or any data type) can be user as a filter parameter. This concept can be used instead of the "DataStructure" concept simply to show an intention and produce a self-documenting code.
**AfterSave** `<SaveMethod>.<ruleName> 'csCodeSnippet'` - CLASS SaveMethodAfterSaveInfo.
**Allow** `<rowPermissionsFilters RowPermissions>.<name> 'filterExpressionFunction'` - CLASS RowPermissionsRuleAllowInfo. BASE CLASS RowPermissionsSingleFunctionRuleInfo: This class is a helper for implementing row permissions rules that are based on a single function that returns the rule's filter expression. Other types if row permissions rules are possible, that do not inherit this class (see RowPermissionsRuleInfo).
**AllowRead** `<rowPermissionsFilters RowPermissions>.<name> 'filterExpressionFunction'` - CLASS RowPermissionsRuleAllowReadInfo. BASE CLASS RowPermissionsSingleFunctionRuleInfo: This class is a helper for implementing row permissions rules that are based on a single function that returns the rule's filter expression. Other types if row permissions rules are possible, that do not inherit this class (see RowPermissionsRuleInfo).
**AllowSave** `<InvalidData>` - CLASS InvalidDataAllowSaveInfo. Modifies the InvalidData concept to suppress data validation on save. Instead, the data validation can be excepted separately as a report.
**AllowWrite** `<rowPermissionsFilters RowPermissions>.<name> 'filterExpressionFunction'` - CLASS RowPermissionsRuleAllowWriteInfo. BASE CLASS RowPermissionsSingleFunctionRuleInfo: This class is a helper for implementing row permissions rules that are based on a single function that returns the rule's filter expression. Other types if row permissions rules are possible, that do not inherit this class (see RowPermissionsRuleInfo).
**AllProperties** `<ComputedFrom>` - CLASS EntityComputedFromAllPropertiesInfo.
**AllProperties** `<History>` - CLASS EntityHistoryAllPropertiesInfo.
**AllProperties** `<Logging>` - CLASS AllPropertiesLoggingInfo.
**AllProperties** `<Persisted>` - CLASS PersistedAllPropertiesInfo.
**AllPropertiesFrom** `<destination DataStructure>.<source DataStructure>` - CLASS AllPropertiesFromInfo.
**AllPropertiesWithCascadeDeleteFrom** `<destination DataStructure>.<source DataStructure>` - CLASS AllPropertiesWithCascadeDeleteFromInfo.
**ApplyFilterOnClientRead** `<DataStructure>.<filterName> 'where'` - CLASS ApplyFilterOnClientReadWhereInfo. The given filter will be automatically applied when executing ReadCommand server command (the command is used in SOAP and REST API).
**ApplyFilterOnClientRead** `<DataStructure>.<filterName>` - CLASS ApplyFilterOnClientReadAllInfo. The given filter will be automatically applied when executing ReadCommand server command (the command is used in SOAP and REST API).
**ApplyOnClientRead** `<ComposableFilterBy>` - CLASS ComposableFilterApplyOnClientReadInfo. The given filter will be automatically applied when executing ReadCommand server command (the command is used in SOAP and REST API).
**ApplyOnClientRead** `<ItemFilter>` - CLASS ItemFilterApplyOnClientReadInfo. The given filter will be automatically applied when executing ReadCommand server command (the command is used in SOAP and REST API).
**ArgumentValidation** `<SaveMethod>.<ruleName> 'csCodeSnippet'` - CLASS ArgumentValidationInfo.
**AutoCode** `<Property>` - CLASS AutoCodePropertyInfo.
**AutoCodeCached** `<ShortString>` - CLASS AutoCodeCachedInfo.
**AutoCodeForEach** `<Property> <group Property>` - CLASS AutoCodeForEachInfo.
**AutoCodeForEachCached** `<ShortString> <group Property>` - CLASS AutoCodeForEachCachedInfo.
**AutodetectSqlDependencies** `<dependent LegacyEntity>` - CLASS AutoLegacyEntityDependsOnInfo.
**AutodetectSqlDependencies** `<dependent SqlFunction>` - CLASS AutoSqlFunctionDependsOnInfo.
**AutodetectSqlDependencies** `<dependent SqlProcedure>` - CLASS AutoSqlProcedureDependsOnInfo.
**AutodetectSqlDependencies** `<dependent SqlQueryable>` - CLASS AutoSqlQueryableDependsOnInfo.
**AutodetectSqlDependencies** `<dependent SqlTrigger>` - CLASS AutoSqlTriggerDependsOnInfo.
**AutodetectSqlDependencies** `<dependent SqlView>` - CLASS AutoSqlViewDependsOnInfo.
**AutodetectSqlDependencies** `<Module>` - CLASS ModuleAutoSqlDependsOnInfo.
**AutoInheritRowPermissions** `<Module>` - CLASS AutoInheritRowPermissionsInfo. Each detail data structure in the module will inherit row permissions from it's mater data structure. Each extension in the module will inherit row permissions from it's base data structure. Row permissions can be inherited from other modules to this module.
**AutoInheritRowPermissionsInternally** `<Module>` - CLASS AutoInheritRowPermissionsInternallyInfo. Each detail data structure in the module will inherit row permissions from it's mater data structure. Each extension in the module will inherit row permissions from it's base data structure. Row permissions will not be inherited from other modules to this module.
**BeforeAction** `<Action>.<ruleName> 'codeSnippet'` - CLASS BeforeActionInfo.
**BeforeQuery** `<Query> 'codeSnippet'` - CLASS BeforeQueryWithParameterInfo.
**Binary** `<DataStructure>.<name>` - CLASS BinaryPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**Bool** `<DataStructure>.<name>` - CLASS BoolPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**Browse** `<Module>.<name> <source DataStructure>` - CLASS BrowseDataStructureInfo.
**CascadeDelete** `<Reference>` - CLASS ReferenceCascadeDeleteInfo. <summary> Automatically deletes detail records when a master record is deleted. </summary><remarks> This feature does not create "on delete cascade" in database unless CommonConcepts.Legacy.CascadeDeleteInDatabase is enabled (since Rhetos v2.11). It is implemented in the application layer, because a database implementation would not execute any business logic that is implemented on the child entity. </remarks>
**CascadeDelete** `<UniqueReference>` - CLASS UniqueReferenceCascadeDeleteInfo. <summary> Automatically deletes the extension records when a master record is deleted. </summary><remarks> This feature does not create "on delete cascade" in database (since Rhetos v2.11, unless CommonConcepts.Legacy.CascadeDeleteInDatabase is enabled). It is implemented in the application layer, because a database implementation would not execute any business logic that is implemented on the extension entity. </remarks>
**ChangesOnBaseItem** `<computation DataStructure>` - CLASS ChangesOnBaseItemInfo.
**ChangesOnChangedItems** `<computation DataStructure>.<dependsOn DataStructure>.<filterType>.<filterFormula>` - CLASS ChangesOnChangedItemsInfo.
**ChangesOnLinkedItems** `<computation DataStructure>.<linkedItemsReference Reference>` - CLASS ChangesOnLinkedItemsInfo. A helper for defining a computation dependency to the detail list, when computing an aggregate.
**ChangesOnReferenced** `<computation DataStructure>.<referencePath>` - CLASS ChangesOnReferencedInfo. A helper for defining a computation dependency to the referenced entity. * The ReferencePath can include the 'Base' reference from extended concepts. * The ReferencePath can target a Polymorphic. This will generate a ChangesOnChangesItems for each Polymorphic implementation.
**Clustered** `<DataStructure>.<propertyNames>` - CLASS SqlIndexClusteredFlatInfo. This is a generic version of the Clustered concept syntax, that helps with DSL parser disambiguation. This concept will be used if the Clustered keyword is placed flat in the Entity. Other concepts with Clustered keyword will be used if the Clustered keyword is nested in a specific indexing concept.
**Clustered** `<SqlIndex>` - CLASS SqlIndexPropertyClusteredInfo.
**Clustered** `<SqlIndexMultiple>` - CLASS SqlIndexClusteredInfo.
**Clustered** `<UniqueMultiple>` - CLASS UniqueClusteredInfo.
**ComposableFilterBy** `<source DataStructure>.<parameter> 'expression'` - CLASS ComposableFilterByInfo.
**ComposableFilterByReferenced** `<source DataStructure>.<parameter> <referenceFromMe Reference> 'subFilterExpression'` - CLASS ComposableFilterByReferencedInfo.
**ComposableFilterByReferenced** `<source DataStructure>.<parameter> <referenceFromMe Reference>` - CLASS ComposableFilterByReferencedNoSubfilterInfo.
**Computed** `<Module>.<name> 'expression'` - CLASS ComputedInfo.
**ComputedFrom** `<target Entity>.<source DataStructure>` - CLASS EntityComputedFromInfo.
**ComputedFrom** `<target Property>.<source Property>` - CLASS PropertyComputedFromInfo.
**ComputeForNewBaseItems** `<ComputedFrom> 'filterSaveExpression'` - CLASS ComputeForNewBaseItemsInfo.
**ComputeForNewBaseItems** `<ComputedFrom>` - CLASS ComputeForNewBaseItemsWithoutFilterInfo.
**ComputeForNewBaseItems** `<Persisted> 'filterSaveExpression'` - CLASS PersistedComputeForNewBaseItemsWithFilterInfo.
**ComputeForNewBaseItems** `<Persisted>` - CLASS PersistedComputeForNewBaseItemsInfo.
**ComputeForNewItems** `<ComputedFrom> 'filterSaveExpression'` - CLASS ComputeForNewItemsInfo.
**ComputeForNewItems** `<ComputedFrom>` - CLASS ComputeForNewItemsWithoutFilterInfo.
**CreatedBy** `<Reference>` - CLASS CreatedByInfo.
**CreationTime** `<DateTime>` - CLASS CreationTimeInfo.
**CustomClaim** `<claimResource>.<claimRight>` - CLASS CustomClaimInfo.
**DataSource** `<ReportData>.<order> <dataSource DataStructure>` - CLASS ReportDataSourceInfo.
**DataSources** `<ReportData> 'dataSources'` - CLASS ReportDataSourcesInfo.
**DataStructure** `<Module>.<name>` - CLASS DataStructureInfo.
**Date** `<DataStructure>.<name>` - CLASS DatePropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**DateRange** `<propertyFrom Property> 'dateToName'` - CLASS DateRangeInfo.
**DateTime** `<DataStructure>.<name>` - CLASS DateTimePropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**DateTimeRange** `<propertyFrom Property> 'dateTimeToName'` - CLASS DateTimeRangeInfo.
**Deactivatable** `<Entity>` - CLASS DeactivatableInfo.
**Decimal** `<DataStructure>.<name>` - CLASS DecimalPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**DefaultLoadFilter** `<ComputedFrom> 'loadFilter'` - CLASS EntityComputedFromDefaultLoadFilterInfo.
**DefaultValue** `<Property> 'expression'` - CLASS DefaultValueInfo.
**Deny** `<rowPermissionsFilters RowPermissions>.<name> 'filterExpressionFunction'` - CLASS RowPermissionsRuleDenyInfo. BASE CLASS RowPermissionsSingleFunctionRuleInfo: This class is a helper for implementing row permissions rules that are based on a single function that returns the rule's filter expression. Other types if row permissions rules are possible, that do not inherit this class (see RowPermissionsRuleInfo).
**DenyRead** `<rowPermissionsFilters RowPermissions>.<name> 'filterExpressionFunction'` - CLASS RowPermissionsRuleDenyReadInfo. BASE CLASS RowPermissionsSingleFunctionRuleInfo: This class is a helper for implementing row permissions rules that are based on a single function that returns the rule's filter expression. Other types if row permissions rules are possible, that do not inherit this class (see RowPermissionsRuleInfo).
**DenySave** `<source DataStructure>.<filterType> 'errorMessage' <dependedProperty Property>` - CLASS DenySaveForPropertyInfo. OBSOLETE: Use InvalidData instead.
**DenySave** `<source DataStructure>.<filterType> 'errorMessage'` - CLASS DenySaveInfo. OBSOLETE: Use InvalidData instead.
**DenyUserEdit** `<DataStructure>` - CLASS DenyUserEditDataStructureInfo.
**DenyUserEdit** `<Property>` - CLASS DenyUserEditPropertyInfo.
**DenyWrite** `<rowPermissionsFilters RowPermissions>.<name> 'filterExpressionFunction'` - CLASS RowPermissionsRuleDenyWriteInfo. BASE CLASS RowPermissionsSingleFunctionRuleInfo: This class is a helper for implementing row permissions rules that are based on a single function that returns the rule's filter expression. Other types if row permissions rules are possible, that do not inherit this class (see RowPermissionsRuleInfo).
**Detail** `<Reference>` - CLASS ReferenceDetailInfo.
**Entity** `<Module>.<name>` - CLASS EntityInfo.
**Entry** `<Hardcoded>.<name>` - CLASS EntryInfo.
**ErrorMetadata** `<InvalidData>.<key> 'value'` - CLASS InvalidDataErrorMetadataInfo.
**Extends** `<extension DataStructure> <base DataStructure>` - CLASS DataStructureExtendsInfo. Inherits the 'UniqueReference' concept and additionally allows cascade delete and automatic inheritance of row permissions. From a business perspective, the main difference between 'Extends' and 'UniqueReference' is that extension is considered a part of the base data structure. In 1:1 relations, the 'Extends' concept is to 'UniqueReference' as 'Reference { Detail; }' is to 'Reference' in 1:N relations.
**ExternalReference** `<Module>.<typeOrAssembly>` - CLASS ModuleExternalReferenceInfo.
**FilterBy** `<source DataStructure>.<parameter> 'expression'` - CLASS FilterByInfo.
**FilterByBase** `<source DataStructure>.<parameter>` - CLASS FilterByBaseInfo.
**FilterByLinkedItems** `<source DataStructure>.<parameter> <referenceToMe Reference>` - CLASS FilterByLinkedItemsInfo.
**FilterByReferenced** `<source DataStructure>.<parameter> <referenceFromMe Reference> 'subFilterExpression'` - CLASS FilterByReferencedInfo.
**FilterByReferenced** `<source DataStructure>.<parameter> <referenceFromMe Reference>` - CLASS FilterByReferencedNoSubfilterInfo.
**From** `<Property> 'path'` - CLASS BrowseFromPropertyInfo.
**Guid** `<DataStructure>.<name>` - CLASS GuidPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**Hardcoded** `<Module>.<name>` - CLASS HardcodedEntityInfo.
**Hierarchy** `<DataStructure>.<name> 'pathName' 'generatePathFrom' 'pathSeparator'` - CLASS HierarchyWithPathInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**Hierarchy** `<DataStructure>.<name>` - CLASS HierarchyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**History** `<Entity>` - CLASS EntityHistoryInfo.
**History** `<Property>` - CLASS EntityHistoryPropertyInfo.
**Implements** `<DataStructure>.<interfaceType>` - CLASS ImplementsInterfaceInfo.
**Implements** `<Is>.<Property> <Entry>` - CLASS SubtypeImplementsReferenceToHardcodedEntityInfo.
**Implements** `<Is>.<Property> 'expression'` - CLASS SubtypeImplementsPropertyInfo.
**ImplementsQueryable** `<DataStructure>.<interfaceType>` - CLASS ImplementsQueryableInterfaceInfo.
**InheritFrom** `<rowPermissionsFilters RowPermissions>.<Reference>` - CLASS RowPermissionsInheritFromReferenceInfo.
**InheritFromBase** `<rowPermissionsFilters RowPermissions>` - CLASS RowPermissionsInheritFromBaseInfo.
**Initialization** `<SaveMethod>.<ruleName> 'csCodeSnippet'` - CLASS SaveMethodInitializationInfo.
**Integer** `<DataStructure>.<name>` - CLASS IntegerPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**IntegerRange** `<propertyFrom Property> 'integerToName'` - CLASS IntegerRangeInfo.
**InvalidData** `<source DataStructure>.<filterType> 'errorMessage' <dependedProperty Property>` - CLASS InvalidDataMarkPropertyInfo. OBSOLETE: Use "MarkProperty" concept instead.
**InvalidData** `<source DataStructure>.<filterType> 'errorMessage'` - CLASS InvalidDataInfo. Simple data validation with a constant error message.
**Is** `<subtype DataStructure>.<supertype Polymorphic>.<implementationName>` - CLASS IsSubtypeOfInfo.
**Is** `<subtype DataStructure>.<supertype Polymorphic>` - CLASS IsSubtypeOfDefaultNameInfo.
**ItemFilter** `<source DataStructure>.<filterName> 'expression'` - CLASS ItemFilterInfo.
**ItemFilterReferenced** `<source DataStructure>.<filterName> <referenceFromMe Reference> 'subFilterExpression'` - CLASS ItemFilterReferencedInfo.
**ItemFilterReferenced** `<source DataStructure>.<filterName> <referenceFromMe Reference>` - CLASS ItemFilterReferencedNoSubfilterInfo.
**KeepSynchronized** `<ComputedFrom> 'filterSaveExpression'` - CLASS KeepSynchronizedInfo.
**KeepSynchronized** `<ComputedFrom>` - CLASS KeepSynchronizedWithoutFilteredSaveInfo.
**KeepSynchronized** `<Persisted> 'filterSaveExpression'` - CLASS PersistedKeepSynchronizedWithFilteredSaveInfo.
**KeepSynchronized** `<Persisted>` - CLASS PersistedKeepSynchronizedInfo.
**KeyProperties** `<ComputedFrom> 'keyProperties'` - CLASS ComputedFromKeyPropertiesInfo.
**KeyProperties** `<Persisted> 'keyProperties'` - CLASS PersistedKeyPropertiesInfo.
**KeyProperty** `<ComputedFrom>` - CLASS KeyPropertyComputedFromInfo.
**KeyPropertyID** `<ComputedFrom>` - CLASS KeyPropertyIDComputedFromInfo.
**LegacyEntity** `<Module>.<name> 'table' 'view'` - CLASS LegacyEntityInfo.
**LegacyEntity** `<Module>.<name> 'table'` - CLASS LegacyEntityWithAutoCreatedViewInfo.
**LegacyProperty** `<Property> 'column'` - CLASS LegacyPropertySimpleInfo.
**LegacyProperty** `<Property> 'columns' 'referencedTable' 'referencedColumns'` - CLASS LegacyPropertyReferenceInfo.
**LegacyPropertyReadOnly** `<Property> 'column'` - CLASS LegacyPropertyReadOnlyInfo.
**LinkedItems** `<DataStructure>.<name> <Reference>` - CLASS LinkedItemsInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**LoadOldItems** `<SaveMethod>` - CLASS LoadOldItemsInfo.
**Lock** `<source DataStructure>.<filterType> 'title' <dependedProperty Property>` - CLASS LockItemsMarkPropertyInfo. Same business logic as LockItemsInfo. The given property is not used in locking, it is just reported to the user (the client application should focus the property).
**Lock** `<source DataStructure>.<filterType> 'title'` - CLASS LockItemsInfo.
**LockExcept** `<source DataStructure>.<filterType> 'title' 'exceptProperties'` - CLASS LockItemsExceptPropertiesInfo.
**LockProperty** `<source Property>.<filterType> 'title'` - CLASS LockPropertyInfo.
**Log** `<Logging>.<Property>` - CLASS PropertyLoggingInfo.
**Logging** `<Entity>` - CLASS EntityLoggingInfo.
**LogReaderAdditionalSource** `<SqlQueryable>.<name> 'snippet'` - CLASS LogReaderAdditionalSourceInfo. A low-level concept that inserts the SQL code snippet to the log reader SqlQueryable at the place of the given tag (an SQL comment).
**LongString** `<DataStructure>.<name>` - CLASS LongStringPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**MarkProperty** `<InvalidData> <markProperty Property>` - CLASS InvalidDataMarkProperty2Info.
**Materialized** `<Polymorphic>` - CLASS PolymorphicMaterializedInfo.
**MaxLength** `<Property> 'length'` - CLASS MaxLengthInfo.
**MaxValue** `<Property> 'value'` - CLASS MaxValueInfo.
**MessageFunction** `<InvalidData> 'messageFunction'` - CLASS InvalidDataMessageFunctionInfo. BASE CLASS InvalidDataMessageInfo: This base class for different implementations of error messages is used to ensure only one implementation will be used on a single InvalidData concept.
**MessageParametersConstant** `<InvalidData> 'messageParameters'` - CLASS InvalidDataMessageParametersConstantInfo. Optimized version of "MessageParameters" concept; no need to query database to retrieve error message parameters. Example: InvalidData with error message 'Maximum value of property {0} is {1}.' may contain MessageParametersConstant '"Age", 200'. By separating the parameters from the error message, only one error message needs to be translated for many different max-value constraints.
**MessageParametersItem** `<InvalidData> 'messageParameters'` - CLASS InvalidDataMessageParametersItemInfo. Use this concept to separate message parameters from the error message, for easier translation to another language. Example: InvalidData with error message 'Maximum value of property {0} is {1}. Current value ({2}) is {3} characters long.' may contain MessageParameters 'item =&gt; new object[] { item.ID, P0 = "Age", P1 = 200, P2 = item.Age, P3 = item.Age.Length }'. By separating the parameters from the error message, only one error message needs to be translated for many different max-value constraints.
**MinLength** `<Property> 'length'` - CLASS MinLengthInfo.
**MinValue** `<Property> 'value'` - CLASS MinValueInfo.
**ModificationTimeOf** `<DateTime>.<modifiedProperty Property>` - CLASS ModificationTimeOfInfo.
**Module** `<name>` - CLASS ModuleInfo.
**Money** `<DataStructure>.<name>` - CLASS MoneyPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**OldDataLoaded** `<SaveMethod>.<ruleName> 'csCodeSnippet'` - CLASS OldDataLoadedInfo.
**OnSaveUpdate** `<SaveMethod>.<ruleName> 'csCodeSnippet'` - CLASS OnSaveUpdateInfo.
**OnSaveValidate** `<SaveMethod>.<ruleName> 'csCodeSnippet'` - CLASS OnSaveValidateInfo.
**Parameter** `<Module>.<name>` - CLASS ParameterInfo. This concept is not necessary for defining a filter. Any data structure (or any data type) can be user as a filter parameter. This concept can be used instead of the "DataStructure" concept simply to show an intention and produce a self-documenting code.
**Persisted** `<Module>.<name> <source DataStructure>` - CLASS PersistedDataStructureInfo.
**PessimisticLocking** `<resource DataStructure>` - CLASS PessimisticLockingInfo.
**PessimisticLockingParent** `<Reference> <detail PessimisticLocking>` - CLASS PessimisticLockingParentInfo.
**Polymorphic** `<Module>.<name>` - CLASS PolymorphicInfo.
**PrerequisiteAllProperties** `<dependsOn Entity>` - CLASS PrerequisiteAllProperties. This concept is used as a placeholder for internal optimization when all properties of an entity are required as a prerequisite for another concept. A dependent object can reference this concept as a dependency, instead of referencing each property individually.
**PropertyFrom** `<destination DataStructure>.<source Property>` - CLASS PropertyFromInfo.
**Query** `<DataStructure>.<parameterType> 'queryImplementation'` - CLASS QueryWithParameterInfo.
**QueryableExtension** `<Module>.<name> <base DataStructure> 'expression'` - CLASS QueryableExtensionInfo.
**Range** `<propertyFrom Property>.<propertyTo Property>` - CLASS RangeInfo.
**Reference** `<DataStructure>.<name> <referenced DataStructure>` - CLASS ReferencePropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**Reference** `<DataStructure>.<name>` - CLASS SimpleReferencePropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**RegExMatch** `<Property> 'regularExpression' 'errorMessage'` - CLASS RegExMatchInfo.
**RegExMatch** `<Property> 'regularExpression'` - CLASS RegExMatchDefaultMessageInfo.
**RegisteredImplementation** `<Implements>` - CLASS RegisteredInterfaceImplementationHelperInfo. Registers the data structure (and it's repository) as the main implementation of the given interface. This allows for type-safe code in external business layer class library to have simple access to the generated data structure's class and the repository using predefined interfaces.
**RegisteredImplementation** `<ImplementsQueryable>` - CLASS RegisteredQueryableInterfaceImplementationHelperInfo. Registers the data structure (and it's repository) as the main implementation of the given interface. This allows for type-safe code in external business layer class library to have simple access to the generated data structure's class and the repository using predefined interfaces.
**RelatedItem** `<Logging>.<table>.<column>.<relation>` - CLASS LoggingRelatedItemInfo.
**ReportData** `<Module>.<name>` - CLASS ReportDataInfo. BASE CLASS Parameter: This concept is not necessary for defining a filter. Any data structure (or any data type) can be user as a filter parameter. This concept can be used instead of the "DataStructure" concept simply to show an intention and produce a self-documenting code.
**ReportFile** `<Module>.<name> 'expression'` - CLASS ReportFileInfo. BASE CLASS Parameter: This concept is not necessary for defining a filter. Any data structure (or any data type) can be user as a filter parameter. This concept can be used instead of the "DataStructure" concept simply to show an intention and produce a self-documenting code.
**RepositoryMember** `<DataStructure>.<name> 'implementation'` - CLASS RepositoryMemberInfo.
**RepositoryUses** `<DataStructure>.<propertyName> 'propertyType'` - CLASS RepositoryUsesInfo. Adds the private C# property to the data structure's repository class. The property value will be resolved from IoC container. It is typically a system component that is required in some function in the repository class (entity filter or action implementation, e.g.).
**Required** `<Property>` - CLASS RequiredPropertyInfo.
**RequiredAllowSave** `<Property>` - CLASS RequiredAllowSaveInfo.
**RowPermissions** `<DataStructure>` - CLASS RowPermissionsPluginableFiltersInfo. The root concept for row permission rules. It allows combining multiple rules and inheriting rules from one data structure to another.
**RowPermissions** `<source DataStructure> 'simplifiedExpression'` - CLASS RowPermissionsInfo.
**RowPermissionsRead** `<source DataStructure> 'simplifiedExpression'` - CLASS RowPermissionsReadInfo.
**RowPermissionsWrite** `<source DataStructure> 'simplifiedExpression'` - CLASS RowPermissionsWriteInfo.
**SamePropertyValue** `<derivedProperty Property>.<baseSelector> <baseProperty Property>` - CLASS SamePropertyValue2Info. OBSOLETE: Use simpler SamePropertyValue syntax with a path to the base property. Used for internal optimizations when a property on one data structure returns the same value as a property on referenced (base or parent) data structure.
**SamePropertyValue** `<derivedProperty Property>.<path>` - CLASS SamePropertyValueInfo.
**SaveMethod** `<Entity>` - CLASS SaveMethodInfo. Represents the Save() method of the entity's repository in the generated business layer object model (ServerDom.*.dll).
**ShortString** `<DataStructure>.<name>` - CLASS ShortStringPropertyInfo. BASE CLASS Property: Property is an abstract concept: there is no ConceptKeyword.
**SingleRoot** `<Hierarchy>` - CLASS HierarchySingleRootInfo.
**SkipRecomputeOnDeploy** `<ComputedFrom>` - CLASS SkipRecomputeOnDeployInfo.
**Snowflake** `<Module>.<name> <source DataStructure>` - CLASS SnowflakeDataStructureInfo. OBSOLETE.
**SqlDefault** `<Property> 'definition'` - CLASS SqlDefaultPropertyInfo.
**SqlDependsOn** `<dependent AnyConcept>.<dependsOn DataStructure>` - CLASS SqlDependsOnDataStructureInfo.
**SqlDependsOn** `<dependent AnyConcept>.<dependsOn Module>` - CLASS SqlDependsOnModuleInfo.
**SqlDependsOn** `<dependent AnyConcept>.<dependsOn Property>` - CLASS SqlDependsOnPropertyInfo.
**SqlDependsOnFunction** `<dependent AnyConcept>.<dependsOn SqlFunction>` - CLASS SqlDependsOnSqlFunctionInfo.
**SqlDependsOnID** `<dependent AnyConcept>.<dependsOn DataStructure>` - CLASS SqlDependsOnIDInfo.
**SqlDependsOnIndex** `<dependent AnyConcept>.<dependsOn SqlIndexMultiple>` - CLASS SqlDependsOnSqlIndexInfo.
**SqlDependsOnSqlObject** `<dependent AnyConcept>.<dependsOn SqlObject>` - CLASS SqlDependsOnSqlObjectInfo.
**SqlDependsOnView** `<dependent AnyConcept>.<dependsOn SqlView>` - CLASS SqlDependsOnSqlViewInfo.
**SqlFunction** `<Module>.<name> 'arguments' 'source'` - CLASS SqlFunctionInfo.
**SqlImplementation** `<Is> 'sqlQuery'` - CLASS SpecificSubtypeSqlViewInfo.
**SqlIndex** `<dataStructure Entity>.<property1 Property>.<property2 Property>.<property3 Property>` - CLASS SqlIndex3Info.
**SqlIndex** `<dataStructure Entity>.<property1 Property>.<property2 Property>` - CLASS SqlIndex2Info.
**SqlIndex** `<Property>` - CLASS SqlIndexInfo.
**SqlIndexMultiple** `<DataStructure>.<propertyNames>` - CLASS SqlIndexMultipleInfo.
**SqlNotNull** `<Property> 'initialValueSqlExpression'` - CLASS SqlNotNullInfo. This concept is intended for internal use only, for other concepts' implementations. Use "Required" or "SystemRequired" instead to implement business requirements.
**SqlObject** `<Module>.<name> 'createSql' 'removeSql'` - CLASS SqlObjectInfo.
**SqlProcedure** `<Module>.<name> 'procedureArguments' 'procedureSource'` - CLASS SqlProcedureInfo.
**SqlQueryable** `<Module>.<name> 'sqlSource'` - CLASS SqlQueryableInfo.
**SqlTrigger** `<DataStructure>.<name> 'events' 'triggerSource'` - CLASS SqlTriggerInfo.
**SqlView** `<Module>.<name> 'viewSource'` - CLASS SqlViewInfo.
**SuppressSynchronization** `<KeepSynchronized>` - CLASS SuppressSynchronizationInfo.
**SystemRequired** `<Property>` - CLASS SystemRequiredInfo.
**Take** `<Browse> 'path'` - CLASS BrowseTakePropertyInfo.
**Take** `<Browse>.<name> 'path'` - CLASS BrowseTakeNamedPropertyInfo.
**Take** `<LoadOldItems>.<path>` - CLASS LoadOldItemsTakeInfo.
**Unique** `<DataStructure>.<property1 Property>.<property2 Property>.<property3 Property>` - CLASS UniqueProperties3Info.
**Unique** `<DataStructure>.<property1 Property>.<property2 Property>` - CLASS UniquePropertiesInfo.
**Unique** `<Property>` - CLASS UniquePropertyInfo.
**UniqueMultiple** `<DataStructure>.<propertyNames>` - CLASS UniqueMultiplePropertiesInfo.
**UniqueReference** `<extension DataStructure> <base DataStructure>` - CLASS UniqueReferenceInfo. The extension data structure has the same ID as the base data structure. Database: The extension's table has a foreign key constraint on its ID column, referencing the base entity's ID column. C# object model: The extension's class has 'Base' navigation property that references the base class. The base class has Extension_* navigation property that references the extension.
**UseExecutionContext** `<ComposableFilterBy>` - CLASS ComposableFilterUseExecutionContextInfo.
**UseExecutionContext** `<computation DataStructure>` - CLASS ComputationUseExecutionContextInfo.
**UseExecutionContext** `<FilterBy>` - CLASS FilterUseExecutionContextInfo.
**UserRequired** `<Property>` - CLASS UserRequiredPropertyInfo. The property value must be provided by the client application.
**Value** `<Entry>.<propertyName> 'value'` - CLASS EntryValueInfo.
**Where** `<Is>.<expression>` - CLASS SubtypeWhereInfo.
**Write** `<DataStructure> 'saveImplementation'` - CLASS WriteInfo.
