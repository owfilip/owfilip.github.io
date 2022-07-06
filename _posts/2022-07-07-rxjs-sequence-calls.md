service.getCar(string: carId): Observable<Car>;
{
  model: 'Super Car',
  manufacturerId: 'car-123'
}
  
service.getManufacturer(string: manufacturerId): Observable<Manufacturer>
{
  name: 'Big factory',
  locationId: 'loc-123'
}

// Two consecutive http calls, second takes parameter from 1st response result
service.getCar(carId).pipe(
  concatMap(
    (carResponse: Car) => service.getManufacturer(carResponse.manufacturerId)
      .pipe(
        map(manufacturerResponse => { return { carResponse, manufacturerResponse } })
  ))
).subscribe(result => {
  result.carResponse.name;
  result.manufacturerResponse.name;
});

// Three consecutive http calls, each 2nd and 3rd call take parameter from the result of response from previous call
service.getLocation(string: locationId): Observable<Location>
{
  name: 'Somewhere'
}
service.getCar(carId).pipe(
  concatMap(
    (carResponse: Car) => service.getManufacturer(carResponse.manufacturerId)
      .pipe(
        concatMap(manufacturerResponse => service.getLocation(manufacturerResponse.locationId)
          .pipe(
            map(locationResponse => { return { carResponse, manufacturerResponse, locationResponse } })
          )
        )
      )
  )
).subscribe(result => {
  result.carResponse.name;
  result.manufacturerResponse.name;
  result.locationResponse.name;
});

  

