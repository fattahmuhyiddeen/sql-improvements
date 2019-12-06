## Time Taken
Time taken to complete this is around 4 hours


## Solutions

How to improve performance of this query.

```
SELECT Jobs.id AS `Jobs__id`,
  Jobs.name AS `Jobs__name`,
  Jobs.media_id AS `Jobs__media_id`,
  Jobs.job_category_id AS `Jobs__job_category_id`, 
  Jobs.job_type_id AS `Jobs__job_type_id`,
  Jobs.description AS `Jobs__description`,
  Jobs.detail AS `Jobs__detail`,
  Jobs.business_skill AS `Jobs__business_skill`,
  Jobs.knowledge AS `Jobs__knowledge`,
  Jobs.location AS `Jobs__location`,
  Jobs.activity AS `Jobs__activity`,
  Jobs.academic_degree_doctor AS `Jobs__academic_degree_doctor`,
  Jobs.academic_degree_master AS `Jobs__academic_degree_master`,
  Jobs.academic_degree_professional AS `Jobs__academic_degree_professional`,Jobs.academic_degree_bachelor AS `Jobs__academic_degree_bachelor`,
  Jobs.salary_statistic_group AS `Jobs__salary_statistic_group`,
  Jobs.salary_range_first_year AS `Jobs__salary_range_first_year`,
  Jobs.salary_range_average AS `Jobs__salary_range_average`,
  Jobs.salary_range_remarks AS `Jobs__salary_range_remarks`,
  Jobs.restriction AS `Jobs__restriction`,
  Jobs.estimated_total_workers AS `Jobs__estimated_total_workers`,
  Jobs.remarks AS `Jobs__remarks`,
  Jobs.url AS `Jobs__url`,
  Jobs.seo_description AS `Jobs__seo_description`,
  Jobs.seo_keywords AS `Jobs__seo_keywords`,
  Jobs.sort_order AS `Jobs__sort_order`,
  Jobs.publish_status AS `Jobs__publish_status`,
  Jobs.version AS `Jobs__version`,
  Jobs.created_by AS `Jobs__created_by`,
  Jobs.created AS `Jobs__created`,
  Jobs.modified AS `Jobs__modified`,
  Jobs.deleted AS `Jobs__deleted`,
  JobCategories.id AS `JobCategories__id`,
  JobCategories.name AS `JobCategories__name`,
  JobCategories.sort_order AS `JobCategories__sort_order`,
  JobCategories.created_by AS `JobCategories__created_by`,
  JobCategories.created AS `JobCategories__created`,
  JobCategories.modified AS `JobCategories__modified`,
  JobCategories.deleted AS `JobCategories__deleted`,
  JobTypes.id AS `JobTypes__id`,
  JobTypes.name AS `JobTypes__name`,
  JobTypes.job_category_id AS `JobTypes__job_category_id`,
  JobTypes.sort_order AS `JobTypes__sort_order`,
  JobTypes.created_by AS `JobTypes__created_by`,
  JobTypes.created AS `JobTypes__created`,
  JobTypes.modified AS `JobTypes__modified`,
  JobTypes.deleted AS `JobTypes__deleted`
FROM jobs Jobs
LEFT JOIN jobs_personalities JobsPersonalities
  ON Jobs.id = (JobsPersonalities.job_id)
LEFT JOIN personalities Personalities
  ON (Personalities.id = (JobsPersonalities.personality_id)
    AND (Personalities.deleted) IS NULL)
LEFT JOIN jobs_practical_skills JobsPracticalSkills
  ON Jobs.id = (JobsPracticalSkills.job_id)
LEFT JOIN practical_skills PracticalSkills
  ON (PracticalSkills.id = (JobsPracticalSkills.practical_skill_id)
    AND (PracticalSkills.deleted) IS NULL)
LEFT JOIN jobs_basic_abilities JobsBasicAbilities
  ON Jobs.id = (JobsBasicAbilities.job_id)
LEFT JOIN basic_abilities BasicAbilities
  ON (BasicAbilities.id = (JobsBasicAbilities.basic_ability_id) AND (BasicAbilities.deleted) IS NULL)
LEFT JOIN jobs_tools JobsTools
  ON Jobs.id = (JobsTools.job_id)
LEFT JOIN affiliates Tools
  ON (Tools.type = 1
    AND Tools.id = (JobsTools.affiliate_id)
    AND (Tools.deleted) IS NULL)
LEFT JOIN jobs_career_paths JobsCareerPaths
  ON Jobs.id = (JobsCareerPaths.job_id)
LEFT JOIN affiliates CareerPaths
  ON (CareerPaths.type = 3
    AND CareerPaths.id = (JobsCareerPaths.affiliate_id)
    AND (CareerPaths.deleted) IS NULL)
LEFT JOIN jobs_rec_qualifications JobsRecQualifications
  ON Jobs.id = (JobsRecQualifications.job_id)
LEFT JOIN affiliates RecQualifications
  ON (RecQualifications.type = 2
    AND RecQualifications.id = (JobsRecQualifications.affiliate_id)
    AND (RecQualifications.deleted) IS NULL)
LEFT JOIN jobs_req_qualifications JobsReqQualifications
  ON Jobs.id = (JobsReqQualifications.job_id)
LEFT JOIN affiliates ReqQualifications
  ON (ReqQualifications.type = 2
    AND ReqQualifications.id = (JobsReqQualifications.affiliate_id)
    AND (ReqQualifications.deleted) IS NULL)
INNER JOIN job_categories JobCategories
  ON (JobCategories.id = (Jobs.job_category_id)
    AND (JobCategories.deleted) IS NULL)
INNER JOIN job_types JobTypes
  ON (JobTypes.id = (Jobs.job_type_id)
    AND (JobTypes.deleted) IS NULL)
WHERE ((JobCategories.name LIKE '%キャビンアテンダント%'
    OR JobTypes.name LIKE '%キャビンアテンダント%'
    OR Jobs.name LIKE '%キャビンアテンダント%'
    OR Jobs.description LIKE '%キャビンアテンダント%'
    OR Jobs.detail LIKE '%キャビンアテンダント%'キャビン
    OR Jobs.business_skill LIKE '%キャビンアテンダント%'
    OR Jobs.knowledge LIKE '%キャビンアテンダント%'
    OR Jobs.location LIKE '%キャビンアテンダント%'
    OR Jobs.activity LIKE '%キャビンアテンダント%'
    OR Jobs.salary_statistic_group LIKE '%キャビンアテンダント%'
    OR Jobs.salary_range_remarks LIKE '%キャビンアテンダント%'
    OR Jobs.restriction LIKE '%キャビンアテンダント%'
    OR Jobs.remarks LIKE '%キャビンアテンダント%'
    OR Personalities.name LIKE '%キャビンアテンダント%'
    OR PracticalSkills.name LIKE '%キャビンアテンダント%'
    OR BasicAbilities.name LIKE '%キャビンアテンダント%'
    OR Tools.name LIKE '%キャビンアテンダント%'
    OR CareerPaths.name LIKE '%キャビンアテンダント%'
    OR RecQualifications.name LIKE '%キャビンアテンダント%' 
    OR ReqQualifications.name LIKE '%キャビンアテンダント%')
    AND publish_status = 1
    AND (Jobs.deleted) IS NULL)
GROUP BY Jobs.id
ORDER BY Jobs.sort_order desc,
    Jobs.id DESC LIMIT 50 OFFSET 0
```

Solutions are as below.

1. Performance of the query can be improved by implementing index on each column that we use to search and filter in each related table.

2. Wherever possible, use `=` instead of `LIKE`. For example, if possible, change `Jobs.activity LIKE '%キャビンアテンダント%'` to `Jobs.activity = 'キャビンアテンダント'` since querying exact value is faster than comparing using `LIKE`

3. Instead of doing join and search on every rows, cache the result first before query to avoid joins for each rows.

```

SET @jobs_personalities_ids = (SELECT GROUP_CONCAT(id) FROM jobs_personalities WHERE deleted IS NULL);
SET @personalities_ids = (SELECT GROUP_CONCAT(id) FROM personalities WHERE name LIKE '%キャビンアテンダント%' AND deleted IS NULL AND personality_id IN (@jobs_personalities_ids));

SET @practical_skills_ids = (SELECT GROUP_CONCAT(id) FROM practical_skills WHERE name LIKE '%キャビンアテンダント%' AND deleted IS NULL);
SET @job_types_ids = (SELECT GROUP_CONCAT(id) FROM job_types WHERE name LIKE '%キャビンアテンダント%' AND deleted IS NULL);
SET @basic_abilities_ids = (SELECT GROUP_CONCAT(id) FROM basic_abilities WHERE name LIKE '%キャビンアテンダント%' AND deleted IS NULL);
SET @jobs_tools_ids = (SELECT GROUP_CONCAT(id) FROM jobs_tools WHERE name LIKE '%キャビンアテンダント%');
SET @affiliates_ids = (SELECT GROUP_CONCAT(id) FROM affiliates WHERE name LIKE '%キャビンアテンダント%' AND deleted IS NULL AND (type=1 OR type=2 OR type=3));
SET @job_categories_ids = (SELECT GROUP_CONCAT(id) FROM job_categories WHERE name LIKE '%キャビンアテンダント%' AND deleted IS NULL);
SET @job_types_ids = (SELECT GROUP_CONCAT(id) FROM job_types WHERE name LIKE '%キャビンアテンダント%' AND deleted IS NULL);

SELECT Jobs.id AS `Jobs__id`,
  Jobs.name AS `Jobs__name`,
  Jobs.media_id AS `Jobs__media_id`,
  Jobs.job_category_id AS `Jobs__job_category_id`, 
  Jobs.job_type_id AS `Jobs__job_type_id`,
  Jobs.description AS `Jobs__description`,
  Jobs.detail AS `Jobs__detail`,
  Jobs.business_skill AS `Jobs__business_skill`,
  Jobs.knowledge AS `Jobs__knowledge`,
  Jobs.location AS `Jobs__location`,
  Jobs.activity AS `Jobs__activity`,
  Jobs.academic_degree_doctor AS `Jobs__academic_degree_doctor`,
  Jobs.academic_degree_master AS `Jobs__academic_degree_master`,
  Jobs.academic_degree_professional AS `Jobs__academic_degree_professional`,Jobs.academic_degree_bachelor AS `Jobs__academic_degree_bachelor`,
  Jobs.salary_statistic_group AS `Jobs__salary_statistic_group`,
  Jobs.salary_range_first_year AS `Jobs__salary_range_first_year`,
  Jobs.salary_range_average AS `Jobs__salary_range_average`,
  Jobs.salary_range_remarks AS `Jobs__salary_range_remarks`,
  Jobs.restriction AS `Jobs__restriction`,
  Jobs.estimated_total_workers AS `Jobs__estimated_total_workers`,
  Jobs.remarks AS `Jobs__remarks`,
  Jobs.url AS `Jobs__url`,
  Jobs.seo_description AS `Jobs__seo_description`,
  Jobs.seo_keywords AS `Jobs__seo_keywords`,
  Jobs.sort_order AS `Jobs__sort_order`,
  Jobs.publish_status AS `Jobs__publish_status`,
  Jobs.version AS `Jobs__version`,
  Jobs.created_by AS `Jobs__created_by`,
  Jobs.created AS `Jobs__created`,
  Jobs.modified AS `Jobs__modified`,
  Jobs.deleted AS `Jobs__deleted`,
  JobCategories.id AS `JobCategories__id`,
  JobCategories.name AS `JobCategories__name`,
  JobCategories.sort_order AS `JobCategories__sort_order`,
  JobCategories.created_by AS `JobCategories__created_by`,
  JobCategories.created AS `JobCategories__created`,
  JobCategories.modified AS `JobCategories__modified`,
  JobCategories.deleted AS `JobCategories__deleted`,
  JobTypes.id AS `JobTypes__id`,
  JobTypes.name AS `JobTypes__name`,
  JobTypes.job_category_id AS `JobTypes__job_category_id`,
  JobTypes.sort_order AS `JobTypes__sort_order`,
  JobTypes.created_by AS `JobTypes__created_by`,
  JobTypes.created AS `JobTypes__created`,
  JobTypes.modified AS `JobTypes__modified`,
  JobTypes.deleted AS `JobTypes__deleted`
FROM jobs Jobs
INNER JOIN job_categories JobCategories
  ON (JobCategories.id = (Jobs.job_category_id)
INNER JOIN job_types JobTypes
  ON (JobTypes.id = (Jobs.job_type_id)
WHERE ((JobCategories.name LIKE '%キャビンアテンダント%'
    OR JobTypes.name LIKE '%キャビンアテンダント%'
    OR Jobs.name LIKE '%キャビンアテンダント%'
    OR Jobs.description LIKE '%キャビンアテンダント%'
    OR Jobs.detail LIKE '%キャビンアテンダント%'キャビン
    OR Jobs.business_skill LIKE '%キャビンアテンダント%'
    OR Jobs.knowledge LIKE '%キャビンアテンダント%'
    OR Jobs.location LIKE '%キャビンアテンダント%'
    OR Jobs.activity LIKE '%キャビンアテンダント%'
    OR Jobs.salary_statistic_group LIKE '%キャビンアテンダント%'
    OR Jobs.salary_range_remarks LIKE '%キャビンアテンダント%'
    OR Jobs.restriction LIKE '%キャビンアテンダント%'
    OR Jobs.remarks LIKE '%キャビンアテンダント%'
    OR Jobs.personality_id IN (@personalities_ids)
    OR Jobs.practical_skill_id IN (@practical_skills_ids)
    OR Jobs.basic_ability_id IN (@basic_abilities_ids)
    OR Jobs.affiliate_id IN (@affiliates_ids))
    AND publish_status = 1
    AND (Jobs.deleted) IS NULL)
ORDER BY Jobs.sort_order desc,
    Jobs.id DESC LIMIT 50 OFFSET 0
```