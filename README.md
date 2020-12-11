# laget.Db.Mongo.Statistics


## Usage
### Counter
#### Document
```c#
[BsonCollection("statistics.counter")]
public class CounterDocument : Counter
{
    [BsonElement("customId")]
    public int CustomId { get; set; }
    [BsonElement("category")]
    public string Category { get; set; }
}
```

### Service
```c#
public class CounterService
{
    readonly CounterRepository<CounterDocument> _repository;

    public CounterService(CounterRepository<CounterDocument> repository)
    {
        _repository = repository;
    }


    public CounterDocument Get(int customId)
    {
        var filter = Builders<CounterDocument>.Filter.Eq(x => x.CustomId, customId);

        return _repository.Get(filter);
    }

    public IEnumerable<CounterDocument> List(string category)
    {
        var filter = Builders<CounterDocument>.Filter.Eq(x => x.Category, category);

        return _repository.Find(filter);
    }

    public IEnumerable<CounterDocument> List(string customId)
    {
        var builder = Builders<CounterDocument>.Filter;

        var filter = builder.In(x => x.CustomId, customId) &
                     builder.Gte(x => x.Date, new DateTime(DateTime.Now.AddYears(-4).Year, 01, 01).Date);

        return _repository.Find(filter);
    }

    public void Increase(CounterDocument entry)
    {
        var builder = Builders<CounterDocument>.Filter;

        var filter = builder.Eq(x => x.CustomId, entry.CustomId) &
                     builder.Eq(x => x.Date, entry.Date);

        if (_repository.Exists(filter))
            _repository.Increase(filter);
        else
            _repository.Insert(entry);
    }

    public void Increase(int customId)
    {
        var entry = new CounterDocument { CustomId = customId };

        var builder = Builders<CounterDocument>.Filter;

        var filter = builder.Eq(x => x.CustomAttribute, entry.CustomAttribute) &
                     builder.Eq(x => x.Date, entry.Date);

        if (_repository.Exists(filter))
            _repository.Increase(filter);
        else
            _repository.Insert(entry);
    }
}
```

### Object
> TODO
```c#
```

### Switch
#### Document
```c#
[BsonCollection("statistics.switches")]
public class SwitchDocument : Switch
{
    [BsonElement("customAttribute")]
    public string CustomAttribute { get;  set; }
}
```

### Service
```c#
public class SwitchService<T>
{
    readonly SwitchRepository<SwitchDocument> _repository;

    public SwitchService(SwitchRepository<SwitchDocument> repository)
    {
        _repository = repository;
    }


    public CounterDocument Get(int customId)
    {
        var filter = Builders<SwitchDocument>.Filter.Eq(x => x.CustomId, customId);

        return _repository.Get(filter);
    }

    public IEnumerable<StateDocument> List(int customId)
    {
        var builder = Builders<StateDocument>.Filter;

        var filter = builder.Eq(x => x.CustomId, customId) &
                        builder.Eq(x => x.Name, typeof(T).Name);

        return _repository.Find(filter);
    }

    public void Set(StateDocument entry)
    {
        var builder = Builders<StateDocument>.Filter;

        var filter = builder.Eq(x => x.CustomId, entry.CampId) &
                        builder.Eq(x => x.Name, typeof(T).Name) &
                        builder.Eq(x => x.DateTime, entry.DateTime);

        _repository.Upsert(filter, entry);
    }

    public void Set(int customId, bool value)
    {
        var entry = new StateDocument { CustomId = customId, Name = typeof(T).Name, On = value };

        var builder = Builders<StateDocument>.Filter;

        var filter = builder.Eq(x => x.CustomId, entry.CustomId) &
                        builder.Eq(x => x.Name, typeof(T).Name) &
                        builder.Eq(x => x.DateTime, entry.DateTime);

        _repository.Upsert(filter, entry);
    }
}
```