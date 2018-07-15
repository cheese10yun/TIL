# Jquery table


```javascript
var table = $('#example').DataTable({
  processing: true,  // show indicator
  serverSide: true,  // serverSide 한번에 가져오는게 아니라 ajax로 필요 부분만 가져올때 사용
  ajax: {
    url: "/admin/users",
    dataType: "json",
    type: "get",
    dataFilter: function (data) {
      data.size = data.length; // size
      data.page = data.start; // page
      data.recordsTotal = data.totalElements; //totla
      data.recordsFiltered = data.totalElements;
      data.data = data.content; //list

      return JSON.stringify(data);
    },
  },
  destroy: true,
  columns: [
    {data: "email"},
    {data: "lastName"},
    {data: "lang"},
    {data: "dob"},
    {data: "mobile"},
    {data: "address1"},
    {data: "passport"},
    {
      data: "created", render: function (data) {
      var date = moment(data).format('YYYY-MM-DD');
      return date.toString();
    }
    },
  ]
})
```
## dataFilter
* response 값과 datatable 관계 설정

## ETC
```
$ brew tap caskroom/versions
$ brew cask install java8
```
